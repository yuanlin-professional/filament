# VulkanPlatformWindows (VulkanPlatformWindows.cpp)

## 文件概述

Windows 平台的 Vulkan 表面创建实现，使用 Win32 API 创建 VkSurfaceKHR。包含针对 NVIDIA 驱动 DPI 感知问题的变通方案。

## 核心类/结构体

### `VulkanPlatformWindows` 类
继承自 VulkanPlatform，实现：
- `getSwapchainInstanceExtensions()` - 返回 `VK_KHR_WIN32_SURFACE_EXTENSION_NAME`
- `createVkSurfaceKHR()` - 使用 `vkCreateWin32SurfaceKHR` 创建 Win32 表面

## 关键实现逻辑

- **DPI 感知变通**: 调用 `SetThreadDpiAwarenessContext(GetWindowDpiAwarenessContext(hwnd))` 将当前线程的 DPI 感知上下文设为目标窗口的上下文。这是为了解决 NVIDIA 驱动在 `vkGetPhysicalDeviceSurfaceCapabilitiesKHR` 中错误使用调用线程 DPI 上下文的 bug
- **表面创建**: 使用 `GetModuleHandle(nullptr)` 获取当前模块句柄，配合 HWND 创建表面

## 依赖关系

- `backend/platforms/VulkanPlatformWindows.h` - 平台接口
- `vulkan/VulkanConstants.h` - VKALLOC 常量
- `bluevk/BlueVK.h` - Vulkan 函数加载
- `windows.h` - Win32 API（条件编译）
