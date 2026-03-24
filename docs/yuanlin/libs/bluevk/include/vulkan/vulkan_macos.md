# vulkan_macos.h

## 文件概述

`vulkan_macos.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 macOS 平台扩展头文件，定义了在 macOS 上通过 MoltenVK 创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_MVK_macos_surface
- **`VkMacOSSurfaceCreateInfoMVK`**: 包含 macOS NSView 指针的 Surface 创建信息
- **`vkCreateMacOSSurfaceMVK`**: 从 macOS 视图创建 Vulkan Surface

## 关键实现逻辑

通过 `VK_USE_PLATFORM_MACOS_MVK` 宏条件编译。此扩展由 MoltenVK 项目定义。注意 Filament 在 macOS 上实际使用的是 `VK_EXT_metal_surface`（定义在 `vulkan_metal.h` 中），此文件主要用于向后兼容。

## 依赖关系

- `vulkan_core.h` -- Vulkan 核心类型
