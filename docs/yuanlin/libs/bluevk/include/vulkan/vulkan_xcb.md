# vulkan_xcb.h

## 文件概述

`vulkan_xcb.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 XCB 平台扩展头文件，定义了在 X11（通过 XCB 库）窗口系统上创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_KHR_xcb_surface
- **`VkXcbSurfaceCreateInfoKHR`**: 包含 `xcb_connection_t` 和 `xcb_window_t`
- **`vkCreateXcbSurfaceKHR`**: 从 XCB 窗口创建 Vulkan Surface
- **`vkGetPhysicalDeviceXcbPresentationSupportKHR`**: 查询物理设备对 XCB 呈现的支持

## 关键实现逻辑

通过 `VK_USE_PLATFORM_XCB_KHR` 宏条件编译。XCB 是 X11 协议的现代 C 绑定库，相比 Xlib 具有更好的性能和更简洁的 API。

## 依赖关系

- `<xcb/xcb.h>` -- XCB 库类型
- `vulkan_core.h` -- Vulkan 核心类型
