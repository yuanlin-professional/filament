# vulkan_win32.h

## 文件概述

`vulkan_win32.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Windows 平台扩展头文件。定义了 Windows 特有的 Vulkan 扩展，包括 Win32 Surface 创建、外部内存/信号量/Fence 的 Win32 Handle 互操作，以及全屏独占模式。

## 核心类/结构体/函数

### VK_KHR_win32_surface
- **`VkWin32SurfaceCreateInfoKHR`**: 包含 `HINSTANCE` 和 `HWND` 的 Surface 创建信息
- **`vkCreateWin32SurfaceKHR`**: 从 Win32 窗口创建 Vulkan Surface

### VK_KHR_external_memory_win32 / VK_KHR_external_fence_win32 / VK_KHR_external_semaphore_win32
- 通过 Win32 HANDLE 在进程间共享 Vulkan 内存、Fence 和 Semaphore

### VK_EXT_full_screen_exclusive
- 全屏独占模式控制，适用于游戏和 VR 场景

## 关键实现逻辑

所有结构和函数通过 `VK_USE_PLATFORM_WIN32_KHR` 宏守护。Windows 平台特有的句柄类型（`HANDLE`、`HINSTANCE`、`HWND`、`LUID` 等）直接在 Vulkan 结构体中使用。

## 依赖关系

- `<windows.h>` -- Windows 类型定义
- `vulkan_core.h` -- Vulkan 核心类型
