# WebGPUPlatformWindows

## 文件概述

Windows 平台的 WebGPU 后端实现，处理 Win32 窗口的 surface 创建和尺寸获取。

## 核心实现

- **适配器选项**: 请求 Vulkan、OpenGL、OpenGLES、D3D12、D3D11 五种后端
- **Surface 创建**: 使用 `wgpu::SurfaceSourceWindowsHWND`，传入 HINSTANCE 和 HWND
- **尺寸获取**: 通过 `GetWindowRect` 获取窗口矩形

## 关键实现逻辑

- DPI 感知修复: 调用 `SetThreadDpiAwarenessContext` 设置当前线程的 DPI 上下文为目标窗口的上下文，绕过 NVIDIA Vulkan 驱动的 DPI 问题

## 依赖关系

- `backend/platforms/WebGPUPlatformWindows.h`
- `Windows.h` - Win32 API
