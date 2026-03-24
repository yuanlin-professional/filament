# CubemapIBL.h / CubemapIBL.cpp

## 文件概述

基于图像的光照（IBL）核心计算库，生成用于 PBR 渲染的预滤波环境贴图和 DFG LUT。实现了 GGX 重要性采样的粗糙度滤波、漫反射辐照度计算和 BRDF 积分查找表生成。

## 核心类/结构体/函数

- **`CubemapIBL`** - IBL 预计算工具类
  - `roughnessFilter()` - 使用 GGX 重要性采样的粗糙度预滤波
  - `diffuseIrradiance()` - 使用余弦重要性采样的漫反射辐照度计算
  - `DFG()` - 生成 DFG（split-sum）查找表
  - `brdf()` - BRDF 调试可视化
  - `Progress` 回调 - 进度通知

## 关键实现逻辑

- **粗糙度滤波**：
  - 使用 Hammersley 序列进行 GGX 重要性采样
  - 预计算采样方向、BRDF 权重和 mip 级别（CacheEntry），按权重排序提高精度
  - LOD 选择：`lod = log4(omegaS/omegaP * K)`，K=4 用于 box filter
  - 随机旋转采样锥以减少可见的结构化伪影
- **DFG 查找表**：
  - `DFV()` - 标准 split-sum：分离 f0 和 f90 项
  - `DFV_Multiscatter()` - 多散射版本：假设 f90=1
  - `DFV_Charlie_Uniform()` - 布料着色用 Charlie 分布
  - 粗糙度映射：`coord = sqrt(linear_roughness)`
- **分布函数**：
  - `DistributionGGX()` - Trowbridge-Reitz 分布
  - `DistributionCharlie()` - 布料微表面分布（Estevez-Kulla 2017）
  - `Visibility()` - Height-correlated Smith GGX
  - `Fresnel()` - Schlick 近似

## 依赖关系

- `ibl/Cubemap.h`, `ibl/CubemapUtils.h`, `ibl/utilities.h` - IBL 基础设施
- `utils/JobSystem.h` - 多线程并行处理
- `math/mat3.h`, `math/scalar.h` - 矩阵和标量运算
