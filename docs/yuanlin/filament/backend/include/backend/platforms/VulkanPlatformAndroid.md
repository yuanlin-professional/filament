# VulkanPlatformAndroid.h

## 文件概述

定义了 `VulkanPlatformAndroid` 类，是 Android 平台上 Vulkan 后端的具体实现。支持 AHardwareBuffer 外部图像、帧时序查询和原生栅栏导出。

## 核心类

### VulkanPlatformAndroid (继承自 VulkanPlatform, AndroidNdk)

**内部类型**:
- `ExternalImageDescAndroid` - 外部图像描述（宽高/格式/用途）
- `ExternalImageVulkanAndroid` - Vulkan Android 外部图像（AHardwareBuffer）

## 主要 API

| 方法 | 说明 |
|------|------|
| `createExternalImage(buffer, sRGB)` | 从 AHardwareBuffer 创建外部图像 |
| `getExternalImageDesc(handle)` | 获取外部图像描述 |
| `extractExternalImageMetadata(image)` | 提取外部图像 Vulkan 元数据 |
| `createVkImageFromExternal(image)` | 从外部图像创建 VkImage |
| `convertSyncToFd(sync, fd)` | 将同步对象转为文件描述符 |
| `getOSVersion()` | 返回 Android SDK 版本 |
| `isCompositorTimingSupported()` | 合成器时序支持 |
| `queryCompositorTiming(...)` / `queryFrameTimestamps(...)` | 时序查询 |

## 依赖关系

- `backend/platforms/VulkanPlatform.h` - 基类
- `backend/platforms/AndroidNdk.h` - NDK 封装
- `backend/DriverEnums.h`
- `android/hardware_buffer.h`
