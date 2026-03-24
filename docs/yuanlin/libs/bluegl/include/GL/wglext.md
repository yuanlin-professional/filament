# wglext.h

## 文件概述

`wglext.h` 是 Khronos Group 官方提供的 Windows 特有 OpenGL 扩展头文件（WGL 扩展），定义了 Windows 平台上 OpenGL 与窗口系统交互的扩展接口。包括像素格式选择、PBuffer 创建、上下文管理、swap control 等功能。

## 核心类/结构体/函数

### 主要扩展类别

- **WGL_ARB_create_context**: 创建 OpenGL 上下文，指定版本和 profile。
- **WGL_ARB_pixel_format**: 高级像素格式查询与选择（`wglChoosePixelFormatARB` 等）。
- **WGL_ARB_pbuffer**: 离屏像素缓冲区（PBuffer）创建与管理。
- **WGL_ARB_render_texture**: 渲染到纹理扩展。
- **WGL_EXT_swap_control**: 垂直同步控制（`wglSwapIntervalEXT`）。
- **WGL_NV_DX_interop**: NVIDIA 的 OpenGL/DirectX 互操作扩展。
- **WGL_AMD_gpu_association**: AMD 多 GPU 关联扩展。

### 句柄类型
- `HPBUFFERARB` -- PBuffer 句柄
- `HGPUNV` -- NVIDIA GPU 句柄
- `HVIDEOOUTPUTDEVICENV` -- 视频输出设备句柄

## 关键实现逻辑

每个 WGL 扩展通过 `#ifndef WGL_XXX` 进行条件保护。定义了 `PFNWGL_xxx_PROC` 函数指针类型，并在 `WGL_WGLEXT_PROTOTYPES` 定义时声明函数原型。版本号为 `WGL_WGLEXT_VERSION 20171125`。

## 依赖关系

- `<windows.h>` -- Windows API 基础类型（`HDC`、`HGLRC`、`HANDLE` 等）
