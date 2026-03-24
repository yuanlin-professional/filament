# BackendUtilsAndroid.h

## 文件概述

提供 Android 平台特有的后端工具函数，用于将 Android `AHardwareBuffer` 的格式和用途映射为 Filament 内部类型。

## 主要 API

| 函数 | 说明 |
|------|------|
| `mapToFilamentFormat(format, isSrgbTransfer)` | 将 AHardwareBuffer 格式映射为 `TextureFormat` |
| `mapToFilamentUsage(usage, format)` | 将 AHardwareBuffer 用途映射为 `TextureUsage` |

## 用途

当 Filament 需要从 Android 硬件缓冲区（AHardwareBuffer）创建纹理时，需要将 Android 系统的格式和用途标志转换为 Filament 内部使用的对应类型。这些函数提供了该转换逻辑。

## 依赖关系

- `backend/DriverEnums.h` - `TextureFormat`、`TextureUsage` 类型
