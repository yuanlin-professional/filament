# VulkanPlatformLinux (VulkanPlatformLinux.cpp)

## 文件概述

Linux/FreeBSD 平台的 Vulkan 表面创建实现，支持 Wayland、XCB 和 Xlib 三种窗口系统。通过编译时宏选择具体的窗口系统后端。

## 核心类/结构体

### `VulkanPlatformLinux` 类
继承自 VulkanPlatform，实现两个核心虚方法：
- `getSwapchainInstanceExtensions()` - 根据编译配置返回所需的 VkSurfaceKHR 扩展名
- `createVkSurfaceKHR()` - 创建特定窗口系统的 VkSurfaceKHR

## 关键实现逻辑

- **Wayland 支持**: 通过 `VK_KHR_WAYLAND_SURFACE_EXTENSION_NAME` 和 `vkCreateWaylandSurfaceKHR` 创建表面，从 wl 结构体解析 display、surface 和尺寸
- **XCB/Xlib 双支持**: 当两者同时编译时，通过 `SWAP_CHAIN_CONFIG_ENABLE_XCB` 标志选择。XCB 使用 `vkCreateXcbSurfaceKHR`，Xlib 使用 `vkCreateXlibSurfaceKHR`
- **动态库加载**: 使用 `dlopen`/`dlsym` 动态加载 libX11.so.6，避免编译时硬依赖
- **全局状态**: X11 连接信息存储在全局 `g_x11_vk` 结构体中，在首次创建表面时初始化

## 依赖关系

- `backend/platforms/VulkanPlatformLinux.h` - 平台接口
- `vulkan/VulkanConstants.h` - VKALLOC 等常量
- `bluevk/BlueVK.h` - Vulkan 函数加载
- `xcb/xcb.h` - XCB 窗口系统（条件编译）
- `X11/Xlib.h` - Xlib 窗口系统（条件编译）
