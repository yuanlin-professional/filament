# vulkan_android.h

## 文件概述

`vulkan_android.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Android 平台 Vulkan 扩展头文件。定义了 Android 特有的 Vulkan 扩展，包括 Android Surface 创建、Android Hardware Buffer 集成和 Android Native Buffer 支持。

## 核心类/结构体/函数

### VK_KHR_android_surface
- **`VkAndroidSurfaceCreateInfoKHR`**: Android Surface 创建信息，包含 `ANativeWindow` 指针
- **`vkCreateAndroidSurfaceKHR`**: 从 Android 原生窗口创建 Vulkan Surface

### VK_ANDROID_external_memory_android_hardware_buffer
- **`VkAndroidHardwareBufferPropertiesANDROID`**: AHardwareBuffer 内存属性查询结果
- **`VkImportAndroidHardwareBufferInfoANDROID`**: 导入 AHardwareBuffer 到 Vulkan 内存
- **`vkGetAndroidHardwareBufferPropertiesANDROID`**: 查询 AHardwareBuffer 属性
- **`vkGetMemoryAndroidHardwareBufferANDROID`**: 从 Vulkan 内存导出 AHardwareBuffer

## 关键实现逻辑

通过条件编译 `VK_USE_PLATFORM_ANDROID_KHR` 控制是否包含。Android 平台上 Vulkan 与 Android NDK 的 `ANativeWindow` 和 `AHardwareBuffer` 直接互操作，支持零拷贝的图形缓冲区共享。

## 依赖关系

- `vulkan_core.h` -- Vulkan 核心类型（通过 `vulkan.h` 间接包含）
- Android NDK 头文件 -- `ANativeWindow` 等前向声明
