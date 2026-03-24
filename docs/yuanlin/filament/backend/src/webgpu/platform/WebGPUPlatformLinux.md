# WebGPUPlatformLinux

## 文件概述

Linux 平台的 WebGPU 后端实现，支持 Wayland、X11/XLib 和 OSMesa 三种窗口系统。

## 核心实现

- **适配器选项**: 请求 Vulkan、OpenGL 和 OpenGLES 后端
- **Surface 创建**:
  - Wayland: 使用 `wgpu::SurfaceSourceWaylandSurface`
  - X11/XLib: 使用 `wgpu::SurfaceSourceXlibWindow`，动态加载 libX11.so.6
- **尺寸获取**:
  - Wayland: 从自定义 `wl` 结构体读取
  - X11: 通过 `XGetWindowAttributes` 获取

## 关键实现逻辑

- X11 库通过 `dlopen`/`dlsym` 动态加载，避免硬链接
- 通过宏 `FILAMENT_SUPPORTS_WAYLAND`/`FILAMENT_SUPPORTS_X11` 等条件编译

## 依赖关系

- `backend/platforms/WebGPUPlatformLinux.h`
- `X11/Xlib.h`（条件编译）
