# FXAA (Fast Approximate Anti-Aliasing)

## 文件概述

`antiAliasing/fxaa/` 目录包含 FXAA 抗锯齿的实现（`fxaa.h`/`fxaa.cpp`/`fxaa.mat`/`fxaa.fs`），这是一种基于图像后处理的快速抗锯齿算法。

## 核心类/结构体

- FXAA 模块通过 `.cpp`/`.h` 文件提供 C++ 端的配置和调度逻辑。
- `fxaa.mat` 定义了后处理材质。
- `fxaa.fs` 包含 FXAA 的核心片段着色器实现。

## 关键实现逻辑

- FXAA 通过分析像素亮度差异来检测边缘，然后沿边缘方向进行亚像素混合以消除锯齿。
- 作为后处理通道运行，不需要额外的几何信息或多采样缓冲。
- 计算开销较低，适合移动端和性能受限的场景。

## 依赖关系

- Filament 后处理管线
- `frameUniforms` - 分辨率等帧级信息
