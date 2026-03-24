# Exposure.cpp

## 文件概述

实现基于物理的曝光（Exposure）计算功能。提供 EV100 值的计算、曝光值的转换，以及亮度（luminance）和照度（illuminance）与 EV100 之间的转换。这些计算遵循摄影测光学标准。

## 核心类/结构体/函数

### `Exposure` 命名空间函数

#### EV100 计算
- **`ev100(Camera&)`** -- 从相机参数计算 EV100。
- **`ev100(aperture, shutterSpeed, sensitivity)`** -- 从光圈、快门速度、ISO 感光度计算 EV100。公式：`log2(N^2/t * 100/S)`
- **`ev100FromLuminance(luminance)`** -- 从场景亮度反推 EV100。使用校准常数 K=12.5。
- **`ev100FromIlluminance(illuminance)`** -- 从照度反推 EV100。使用校准常数 C=250。

#### 曝光值
- **`exposure(Camera&)` / `exposure(aperture, shutterSpeed, sensitivity)` / `exposure(ev100)`** -- 计算曝光系数。公式：`1 / (1.2 * 2^EV100)`。用于片段着色器中将入射亮度归一化到传感器饱和亮度。

#### 亮度
- **`luminance(Camera&)` / `luminance(aperture, shutterSpeed, sensitivity)` / `luminance(ev100)`** -- 计算最大饱和亮度。公式：`2^(EV100-3)`。

#### 照度
- **`illuminance(Camera&)` / `illuminance(aperture, shutterSpeed, sensitivity)` / `illuminance(ev100)`** -- 计算照度值。公式：`2.5 * 2^EV100`。

## 关键实现逻辑

- **饱和度模型**：使用 ISO 12232 的饱和度方法（`S_sat = 78 / H_sat`），其中 78 这个值使 18% 反射率的灰卡在标准曝光下产生约 12.7% 饱和度的图像值。
- **优化合并**：接受光圈/快门/ISO 参数的版本通过合并计算避免了额外的 `pow`/`log2` 调用。
- **物理精确**：所有公式都基于摄影测光学标准，参考 Wikipedia 的 Exposure value 和 Film speed 页面。

## 依赖关系

- `filament/Exposure.h` -- 公共头文件
- `details/Camera.h` -- 获取相机曝光参数

## 被引用关系

被渲染器（Renderer）在帧渲染时调用，用于计算当前帧的曝光参数，影响色调映射和自动曝光控制。
