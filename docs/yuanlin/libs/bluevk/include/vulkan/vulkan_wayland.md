# vulkan_wayland.h

## 文件概述

`vulkan_wayland.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Wayland 平台扩展头文件，定义了在 Wayland 窗口系统上创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_KHR_wayland_surface
- **`VkWaylandSurfaceCreateInfoKHR`**: 包含 `wl_display` 和 `wl_surface` 指针
- **`vkCreateWaylandSurfaceKHR`**: 从 Wayland surface 创建 Vulkan Surface
- **`vkGetPhysicalDeviceWaylandPresentationSupportKHR`**: 查询物理设备对 Wayland 呈现的支持

## 关键实现逻辑

通过 `VK_USE_PLATFORM_WAYLAND_KHR` 宏条件编译。Wayland 是 Linux 上替代 X11 的现代窗口系统协议，此扩展实现 Vulkan 与 Wayland 合成器的直接交互。

## 依赖关系

- Wayland 客户端库头文件（`wl_display`、`wl_surface` 前向声明）
- `vulkan_core.h` -- Vulkan 核心类型
