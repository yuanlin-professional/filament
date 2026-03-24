# CubemapSH.h / CubemapSH.cpp

## 文件概述

球谐函数（Spherical Harmonics）计算库，用于立方体贴图的球谐分解和重建。支持辐照度卷积、SH 旋转、窗函数去振铃（deringing），以及为着色器预缩放 SH 系数。

## 核心类/结构体/函数

- **`CubemapSH`** - 球谐函数工具类
  - `computeSH()` - 将立方体贴图分解为 SH 系数（可选辐照度卷积）
  - `renderSH()` - 将 SH 系数渲染回立方体贴图
  - `windowSH()` - 应用 sinc 窗函数去振铃
  - `preprocessSHForShader()` - 为着色器预乘多项式系数和 1/PI
  - `renderPreScaledSH3Bands()` - 渲染预缩放的 3 阶段 SH（着色器使用的公式）
  - `rotateShericalHarmonicBand1/2()` - 旋转 1 阶和 2 阶 SH

## 关键实现逻辑

- **SH 基函数计算**：使用关联 Legendre 多项式递推（`computeShBasis`），避免 sin/cos 调用
- **缩放因子**：`Kml = sqrt((2l+1)/4pi) * sqrt((l-|m|)!/(l+|m|)!)`
- **截断余弦 SH**：`computeTruncatedCosSh()` 用于辐照度卷积的预计算系数
- **去振铃（Deringing）**：
  - 基于 Peter-Pike Sloan 的论文，使用 sinc 窗函数 4 次方
  - 自动模式：通过二分搜索找到使 SH 重建非负的最小窗口
  - `shmin()` 函数解析计算 3 阶 SH 的最小值（含旋转对齐优化和 Newton 迭代）
- **SH 旋转**：Band 1 和 Band 2 通过矩阵变换实现，利用预计算的逆矩阵优化

## 依赖关系

- `ibl/Cubemap.h`, `ibl/CubemapUtils.h`, `ibl/utilities.h` - IBL 基础设施
- `utils/JobSystem.h`, `utils/Log.h` - 多线程和日志
- `math/mat3.h`, `math/mat4.h` - 矩阵运算
