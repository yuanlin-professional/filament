# vulkan_xlib_xrandr.h

## 文件概述

`vulkan_xlib_xrandr.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Xlib+XRandR 扩展头文件，定义了通过 Xlib 和 XRandR 扩展获取和管理显示输出的 Vulkan 扩展。

## 核心类/结构体/函数

### VK_EXT_acquire_xlib_display
- **`vkAcquireXlibDisplayEXT`**: 独占获取一个 X11 显示输出
- **`vkGetRandROutputDisplayEXT`**: 从 XRandR RROutput 获取对应的 VkDisplayKHR

## 关键实现逻辑

通过 `VK_USE_PLATFORM_XLIB_XRANDR_EXT` 宏条件编译。此扩展允许 Vulkan 应用直接控制显示输出，绕过窗口管理器，适用于需要独占显示设备的场景（如 VR 头显）。

## 依赖关系

- `<X11/Xlib.h>` -- Xlib 类型
- `<X11/extensions/Xrandr.h>` -- XRandR 扩展类型
- `vulkan_core.h` -- Vulkan 核心类型
