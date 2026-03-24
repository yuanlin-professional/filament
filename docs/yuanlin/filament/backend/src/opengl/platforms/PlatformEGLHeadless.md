# PlatformEGLHeadless.cpp

## 文件概述

无头（Headless）EGL 平台实现，用于无窗口的离屏渲染场景（如服务端渲染、自动化测试）。继承自 PlatformEGL，使用 EGL Surfaceless 或 Pbuffer 进行渲染。

## 核心类/结构体

### `PlatformEGLHeadless`
继承 `PlatformEGL`。

### 关键方法
- `createDriver()`: 初始化 EGL，支持 GLES 和桌面 GL（通过 `isOpenGL()` 区分）。使用 `eglBindAPI` 绑定正确的 API。
- `isOpenGL()`: 根据编译配置返回是否使用桌面 GL。

## 关键实现逻辑

- 通过 `BACKEND_OPENGL_VERSION_GL` 宏区分 GLES 和桌面 GL。
- 在无显示器环境下使用 EGL 的设备查询功能（`EGL_EXT_device_enumeration`）。
- 支持 BlueGL 作为桌面 GL 的函数加载器。

## 依赖关系

- `backend/platforms/PlatformEGLHeadless.h`
- `backend/platforms/PlatformEGL.h` - 父类
- `bluegl/BlueGL.h` - 桌面 GL 函数加载
- `EGL/egl.h`, `EGL/eglext.h`
