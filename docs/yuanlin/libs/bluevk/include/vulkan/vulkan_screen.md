# vulkan_screen.h

## 文件概述

`vulkan_screen.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 QNX Screen 平台扩展头文件，定义了在 QNX 实时操作系统的 Screen 窗口系统上创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_QNX_screen_surface
- **`VkScreenSurfaceCreateInfoQNX`**: 包含 `_screen_context` 和 `_screen_window` 指针
- **`vkCreateScreenSurfaceQNX`**: 从 QNX Screen 窗口创建 Vulkan Surface
- **`vkGetPhysicalDeviceScreenPresentationSupportQNX`**: 查询 Screen 呈现支持

## 关键实现逻辑

通过 `VK_USE_PLATFORM_SCREEN_QNX` 宏条件编译。QNX 是用于汽车和嵌入式系统的实时操作系统。

## 依赖关系

- `<screen/screen.h>` -- QNX Screen 库类型
- `vulkan_core.h` -- Vulkan 核心类型
