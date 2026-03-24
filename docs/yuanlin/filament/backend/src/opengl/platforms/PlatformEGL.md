# PlatformEGL.cpp

## 文件概述

EGL 平台的基础实现，是 Android 和 Linux EGL 平台的父类。负责 EGL 显示初始化、上下文创建、交换链管理、围栏同步和 EGL 扩展函数的动态加载。

## 核心类/结构体

### `PlatformEGL`
所有 EGL 平台的基类。

**EGL 扩展函数指针**（在 `glext` 命名空间中）:
- `eglCreateSyncKHR` / `eglDestroySyncKHR` / `eglClientWaitSyncKHR`: 同步围栏。
- `eglCreateImageKHR` / `eglDestroyImageKHR`: EGL 图像。

### 关键方法
- `createDriver()`: 初始化 EGL 显示、选择配置、创建上下文和 Pbuffer 表面。
- `makeCurrent()`: 调用 `eglMakeCurrent` 切换上下文。
- `commit()`: 调用 `eglSwapBuffers` 提交帧。
- `createFence()` / `waitFence()`: 使用 `EGL_KHR_fence_sync` 实现 GPU 同步。
- `createContext()`: 创建共享 EGL 上下文（用于着色器编译线程池）。

### 辅助函数
- `logEglError()` / `getEglErrorName()`: EGL 错误日志和名称映射。

## 关键实现逻辑

- 使用 `eglGetProcAddress` 动态加载 EGL 扩展函数。
- 支持受保护内容上下文（`EGL_PROTECTED_CONTENT_EXT`）。
- 支持 sRGB 交换链（`EGL_GL_COLORSPACE_SRGB_KHR`）。
- Android 上通过 `EGL_CONTEXT_OPENGL_BACKWARDS_COMPATIBLE_ANGLE` 配置 ANGLE 兼容性。

## 依赖关系

- `backend/platforms/PlatformEGL.h`
- `EGL/egl.h`, `EGL/eglext.h`
- `GLUtils.h` - GL 错误检查
- `backend/Platform.h`, `backend/DriverEnums.h`
