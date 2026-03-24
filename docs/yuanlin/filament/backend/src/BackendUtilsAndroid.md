# BackendUtilsAndroid

## 文件概述

`BackendUtilsAndroid.cpp` 提供了 Android 平台特有的 `AHardwareBuffer` 格式到 Filament 纹理格式和使用标志的映射函数。用于将外部硬件缓冲区导入 Filament 渲染管线时进行格式转换。

## 核心类/结构体

本文件不定义类，提供两个独立映射函数。

## 关键实现逻辑

### mapToFilamentFormat()
- 将 `AHARDWAREBUFFER_FORMAT_*` 常量映射为 `TextureFormat` 枚举。
- 支持 sRGB 传输函数：当 `isSrgbTransfer` 为 true 时，RGB8/RGBA8 映射为 SRGB8/SRGB8_A8。
- 覆盖常见格式：RGBA8、RGB8、RGB565、RGBA16F、RGB10_A2、各种深度/模板格式、YUV420 及 R8。
- 不支持的格式返回 `TextureFormat::UNUSED`。

### mapToFilamentUsage()
- 将 `AHARDWAREBUFFER_USAGE_*` 标志映射为 `TextureUsage` 位掩码。
- `GPU_SAMPLED_IMAGE` -> `SAMPLEABLE`
- `GPU_FRAMEBUFFER` -> 根据格式自动判断为 `DEPTH_ATTACHMENT`、`STENCIL_ATTACHMENT` 或 `COLOR_ATTACHMENT`
- `GPU_DATA_BUFFER` -> `UPLOADABLE`
- `PROTECTED_CONTENT` -> `PROTECTED`
- 如果没有匹配的 usage 标志，默认设置为 `COLOR_ATTACHMENT | SAMPLEABLE`。

## 依赖关系

- `private/backend/BackendUtilsAndroid.h` - 头文件声明
- `<android/hardware_buffer.h>` - AHardwareBuffer 格式和使用标志常量
- `backend/DriverEnums.h`（隐式） - TextureFormat、TextureUsage 等枚举定义
