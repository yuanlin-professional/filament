# PlatformGLX.cpp

## 文件概述

Linux X11 平台的 GLX 上下文管理实现，通过动态加载 libGL.so 和 libX11.so 创建和管理 OpenGL 上下文。

## 核心类/结构体

### `GLXFunctions`
存储动态加载的 GLX 函数指针：
- `chooseFbConfig`, `createContext`, `createPbuffer`, `destroyPbuffer`, `setCurrentContext`
- `destroyContext`, `swapBuffers`

### `PlatformGLX`（通过实现文件推断）
- 使用 `dlopen` 动态加载 `libGL.so.1` 和 `libX11.so.6`。
- 使用 `glXCreateContextAttribsARB` 创建 OpenGL 核心配置文件上下文。
- 使用 Pbuffer 作为离屏渲染表面。

## 关键实现逻辑

- 完全动态加载 GLX 和 X11 函数，避免编译时依赖。
- 支持创建共享上下文用于着色器编译线程池。
- 通过 `glXChooseFBConfig` 选择合适的帧缓冲配置。

## 依赖关系

- `backend/platforms/PlatformGLX.h`
- `X11/Xlib.h` - X Window 系统
- `GL/glx.h`, `GL/glxext.h` - GLX 扩展
- `dlfcn.h` - 动态库加载
