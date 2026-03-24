# vulkan_xlib.h

## 文件概述

`vulkan_xlib.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Xlib 平台扩展头文件，定义了在 X11（通过 Xlib 库）窗口系统上创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_KHR_xlib_surface
- **`VkXlibSurfaceCreateInfoKHR`**: 包含 `Display*` 和 `Window`（X11 类型）
- **`vkCreateXlibSurfaceKHR`**: 从 Xlib 窗口创建 Vulkan Surface
- **`vkGetPhysicalDeviceXlibPresentationSupportKHR`**: 查询物理设备对 Xlib 呈现的支持

## 关键实现逻辑

通过 `VK_USE_PLATFORM_XLIB_KHR` 宏条件编译。Xlib 是最传统的 X11 客户端库，广泛用于 Linux 桌面环境。

## 依赖关系

- `<X11/Xlib.h>` -- Xlib 库类型
- `vulkan_core.h` -- Vulkan 核心类型
