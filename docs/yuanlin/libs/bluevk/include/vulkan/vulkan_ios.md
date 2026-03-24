# vulkan_ios.h

## 文件概述

`vulkan_ios.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 iOS 平台扩展头文件，定义了在 iOS 上通过 MoltenVK 创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_MVK_ios_surface
- **`VkIOSSurfaceCreateInfoMVK`**: 包含 iOS UIView 指针的 Surface 创建信息
- **`vkCreateIOSSurfaceMVK`**: 从 iOS 视图创建 Vulkan Surface

## 关键实现逻辑

通过 `VK_USE_PLATFORM_IOS_MVK` 宏条件编译。此扩展由 MoltenVK 项目定义，用于在 iOS 的 Metal 后端之上提供 Vulkan 支持。`pView` 参数通常是指向 `UIView` 的指针。

## 依赖关系

- `vulkan_core.h` -- Vulkan 核心类型
