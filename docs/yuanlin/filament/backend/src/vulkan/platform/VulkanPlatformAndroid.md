# VulkanPlatformAndroid (VulkanPlatformAndroid.cpp)

## 文件概述

Android 平台特定的 Vulkan 实现，处理 Android 原生窗口表面创建、AHardwareBuffer 外部图像导入、YCbCr 格式转换、帧时序查询以及 Fence 到文件描述符的转换。

## 核心类/结构体

### `VulkanPlatformAndroid` 类
继承自 VulkanPlatform，Android 特化实现：
- `createVkSurfaceKHR()` - 使用 `vkCreateAndroidSurfaceKHR` 创建 Android 表面
- `createExternalImage()` - 从 AHardwareBuffer 创建外部图像句柄
- `extractExternalImageMetadata()` - 提取 AHB 的 VkFormat、用途、YCbCr 属性等元数据
- `createVkImageFromExternal()` - 从外部图像创建 VkImage 和绑定设备内存
- `convertSyncToFd()` - 使用 `vkGetFenceFdKHR` 将 Fence 转为 sync_fd

### `ExternalImageVulkanAndroid` 结构体
持有 AHardwareBuffer 指针和 sRGB 标志，析构时释放 AHB 引用。

## 关键实现逻辑

- **AHB 格式映射**: `getVKFormatAndUsage()` 将 AHardwareBuffer 格式映射为 VkFormat 和 VkImageUsageFlags
- **外部图像创建**: 使用 `VK_EXTERNAL_MEMORY_HANDLE_TYPE_ANDROID_HARDWARE_BUFFER_BIT_ANDROID` 导入 AHB 内存
- **YCbCr 处理**: 当格式为 UNDEFINED 或 YCbCr 转换格式时，使用 externalFormat 和外部采样器
- **sRGB 格式列表**: 对 sRGB 格式使用 `VK_IMAGE_CREATE_MUTABLE_FORMAT_BIT` 和 `VkImageFormatListCreateInfo`
- **RenderDoc 兼容**: `selectMemoryTypeForExternalImage()` 中包含 RenderDoc 回放兼容逻辑
- **帧时序**: 集成 AndroidFrameCallback 和 NativeWindow API 获取合成器时序和帧时间戳

## 依赖关系

- `backend/platforms/VulkanPlatformAndroid.h` - Android 平台接口
- `vulkan/VulkanContext.h` - 内存类型选择
- `vulkan/platform/VulkanPlatformSwapChainImpl.h` - 交换链基类
- `vulkan/utils/Image.h` - YCbCr 格式检测
- `AndroidFrameCallback.h` / `AndroidNativeWindow.h` - Android 原生窗口交互
- `android/hardware_buffer.h` - AHardwareBuffer API
