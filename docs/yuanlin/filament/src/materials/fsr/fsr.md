# FSR (FidelityFX Super Resolution)

## 文件概述

`fsr/` 目录包含 AMD FidelityFX Super Resolution 1.0 的实现（`fsr.h`/`fsr.cpp` 及多个 `.mat`/`.h`/`.fs` 文件），提供空间分辨率上采样功能。

## 材质文件

| 文件 | 说明 |
|------|------|
| `fsr_easu.mat` | EASU（Edge-Adaptive Spatial Upsampling）通道 |
| `fsr_easu_mobile.mat` | EASU 移动端变体 |
| `fsr_easu_mobileF.mat` | EASU 移动端浮点变体 |
| `fsr_rcas.mat` | RCAS（Robust Contrast-Adaptive Sharpening）通道 |

## 核心文件

- `ffx_a.h`: FidelityFX 通用数学库
- `ffx_fsr1.h`: FSR 1.0 核心算法头文件
- `ffx_fsr1_mobile.fs`: 移动端优化的 FSR 实现

## 关键实现逻辑

- **EASU**: 边缘自适应空间上采样，分析周围像素的边缘方向，沿边缘进行高质量插值。
- **RCAS**: 鲁棒对比度自适应锐化，在上采样后恢复细节清晰度。
- 两个通道组合实现低分辨率渲染 + 高质量上采样，提升性能同时保持画质。

## 依赖关系

- `license.txt` - AMD FidelityFX 许可证
- Filament 后处理管线
