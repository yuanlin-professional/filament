# ColorGrading

## 文件概述

`FColorGrading` 是 `ColorGrading` 公共 API 的私有实现类，负责生成颜色分级 3D LUT（Look-Up Table）纹理。支持丰富的后处理色彩调整参数，包括白平衡、通道混合、色调映射、ASC CDL、曲线调整等，并生成 GPU 纹理供着色器使用。

## 核心类/结构体

### `FColorGrading`
- 继承自 `ColorGrading`
- 持有 LUT 纹理句柄 `mLutHandle`
- 支持 1D LUT（简单色调映射）和 3D LUT（完整颜色分级）两种模式

### `ColorGrading::BuilderDetails`
- 包含所有颜色调整参数：曝光、白平衡、通道混合、阴影/中间调/高光、ASC CDL、对比度、饱和度、鲜艳度、曲线等
- 支持质量级别（LOW/MEDIUM/HIGH/ULTRA）控制 LUT 维度和格式

## 关键实现逻辑

- **色彩处理管线** -- LogC 编码 -> 曝光调整 -> Purkinje 偏移 -> Rec.2020 色彩空间 -> 白平衡 -> 通道混合 -> 色调范围 -> ASC CDL -> 对比度 -> 鲜艳度/饱和度 -> 曲线 -> 色调映射 -> OETF
- **Purkinje 效应** -- 实现暗适应色彩偏移，模拟人眼在低光环境下向蓝色端的感知偏移
- **亮度缩放** -- 基于 Troy Sobotka 的 EVILS 算法，保持色调映射时的色度比例
- **多线程生成** -- 3D LUT 使用 JobSystem 并行生成各切片，Android 上约 3-6ms
- **格式选择** -- 支持 RGB10_A2（整数）和 RGBA16F（浮点）两种 LUT 纹理格式

## 依赖关系

- `ColorSpaceUtils.h` -- 色彩空间转换工具（sRGB/Rec.2020/LogC 等）
- `details/Engine.h`、`details/Texture.h` -- 引擎和纹理管理
- `utils/JobSystem.h` -- 多线程任务系统

## 与公共 API 的关系

直接对应 `filament::ColorGrading`。通过 `ColorGrading::Builder` 配置参数并构建，设置到 `View` 上以应用后处理颜色分级效果。
