# PlatformEGL.h

## 文件概述

定义了 `PlatformEGL` 类，是基于 EGL 的 OpenGL/OpenGL ES 平台实现。这是最功能完整的 OpenGL 平台类之一，支持受保护上下文、sRGB SwapChain、MSAA、Fence、外部纹理等功能。

## 核心类

### PlatformEGL (继承自 OpenGLPlatform)

**内部类型**:
- `Config` - EGL 配置属性辅助类（键值对列表）
- `SwapChainEGL` - EGL SwapChain 实现（含 EGLSurface）
- `ExternalImageEGL` - EGL 外部图像（含 EGLImageKHR）

**扩展检测**:
```cpp
ext.gl.OES_EGL_image_external_essl3
ext.egl.ANDROID_recordable / KHR_create_context / KHR_gl_colorspace 等
```

## 主要 API

| 方法 | 说明 |
|------|------|
| `createExternalImage(eglImage)` | 从 EGLImageKHR 创建外部图像 |
| `isOpenGL()` | 是否为 OpenGL（非 ES）平台 |
| `createDriver(sharedContext, config)` | 初始化 EGL 并创建驱动 |
| `isProtectedContextSupported()` | 受保护上下文支持 |
| `isSRGBSwapChainSupported()` | sRGB SwapChain 支持 |
| `isMSAASwapChainSupported(samples)` | MSAA SwapChain 支持 |
| `makeCurrent(type, draw, read)` | 绑定上下文 |
| Fence 系列方法 | 创建/销毁/等待 Fence |
| `getEglDisplay()` / `getEglConfig()` | 获取 EGL 对象 |
| `logEglError(name)` / `clearGlError()` | 错误处理工具 |

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `backend/DriverEnums.h` / `backend/Platform.h`
- `EGL/egl.h` / `EGL/eglext.h` - EGL API
- `utils/compiler.h` / `utils/Invocable.h`
