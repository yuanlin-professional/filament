# fsr.h / fsr.cpp

## 文件概述

封装 AMD FidelityFX Super Resolution (FSR) 1.0 的 CPU 端初始化逻辑。提供 EASU（Edge Adaptive Spatial Upsampling，边缘自适应空间上采样）和 RCAS（Robust Contrast-Adaptive Sharpening，鲁棒对比度自适应锐化）两个阶段的参数设置。

## 核心类/结构体/函数

### 配置结构体

- **`FSRScalingConfig`** -- EASU 缩放配置：后端类型、输入视口区域、输入/输出分辨率。
- **`FSRSharpeningConfig`** -- RCAS 锐化配置：锐化强度（`sharpness`，0 为最大锐化）。
- **`FSRUniforms`** -- 传递给着色器的 Uniform 数据：`EasuCon0-3`（EASU 参数）和 `RcasCon`（RCAS 参数）。

### 函数

- **`FSR_ScalingSetup(FSRUniforms*, FSRScalingConfig)`** -- 计算 EASU 阶段的着色器参数。调用 AMD 的 `FsrEasuConOffset` 函数。
- **`FSR_SharpeningSetup(FSRUniforms*, FSRSharpeningConfig)`** -- 计算 RCAS 阶段的着色器参数。调用 AMD 的 `FsrRcasCon` 函数。

## 关键实现逻辑

- **后端适配**：EASU 的 Y 轴偏移需要根据图形 API 后端进行调整。Metal、Vulkan 和 WebGPU 使用左上角原点（需要翻转 Y 坐标），OpenGL 使用左下角原点。
- **AMD FFX 集成**：通过定义 `A_CPU=1` 和 `FSR_EASU_F=1`、`FSR_RCAS_F=1` 宏来包含 AMD 的 `ffx_a.h` 和 `ffx_fsr1.h` 头文件，这些文件提供了 CPU 端的参数计算函数。
- **编译警告抑制**：对 AMD 头文件的未使用函数和忽略限定符警告进行了抑制。

## 依赖关系

- `filament/Viewport.h` -- 视口定义
- `math/vec4.h` -- 向量类型
- `materials/fsr/ffx_a.h`、`materials/fsr/ffx_fsr1.h` -- AMD FidelityFX 库头文件

## 被引用关系

被后处理管线中的 FSR 上采样阶段使用，在需要将低分辨率渲染结果上采样到目标分辨率时调用。
