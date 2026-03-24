# Exposure.h

## 文件概述

提供基于物理相机模型的曝光计算工具函数，包括 EV100、光度曝光、亮度和照度的转换。

## 核心命名空间

- **`Exposure`** - 命名空间，包含一系列曝光相关的工具函数。

## 主要 API

| 函数 | 说明 |
|------|------|
| `ev100(camera)` | 从相机参数计算 EV100 |
| `ev100(aperture, shutterSpeed, sensitivity)` | 从曝光参数计算 EV100 |
| `ev100FromLuminance(luminance)` | 从亮度计算 EV100 |
| `ev100FromIlluminance(illuminance)` | 从照度计算 EV100 |
| `exposure(camera)` / `exposure(ev100)` | 计算光度曝光值 |
| `luminance(camera)` / `luminance(ev100)` | 计算入射亮度 (cd/m^2) |
| `illuminance(camera)` / `illuminance(ev100)` | 计算照度 (lux) |

## 依赖关系

- `utils/compiler.h`

## 使用示例

```cpp
float ev = Exposure::ev100(16.0f, 1.0f/125.0f, 100.0f);
float lux = Exposure::illuminance(ev);
```
