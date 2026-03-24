# PlatformEGLAndroid.cpp

## 文件概述

Android 平台的 EGL 特化实现，在 PlatformEGL 基础上增加 Android 特有的功能：外部流（SurfaceTexture）、AHardwareBuffer 支持、帧时间戳、受保护内容和性能提示管理。

## 核心类/结构体

### `PlatformEGLAndroid`
继承 `PlatformEGL`。

**Android 专有 EGL 扩展**:
- `eglGetNativeClientBufferANDROID`: AHardwareBuffer 到 EGLClientBuffer 转换。
- `eglPresentationTimeANDROID`: 设置帧呈现时间。
- `eglGetFrameTimestampsANDROID`: 获取帧时间戳。
- `eglDupNativeFenceFDANDROID`: 原生围栏文件描述符。

### 关键方法
- `createDriver()`: 在 PlatformEGL 基础上初始化 Android 特有的 EGL 扩展、外部流管理器和帧回调。
- `createStream()` / `attach()` / `updateTexImage()`: 委托给 `ExternalStreamManagerAndroid`。
- `createExternalImageTexture()`: 通过 EGLImage 和 AHardwareBuffer 创建外部纹理。
- `beginFrame()` / `endFrame()`: 管理帧时间戳和性能提示。
- `isProtectedContextSupported()`: 检查 EGL 受保护内容扩展。

## 关键实现逻辑

- 使用 `AHardwareBuffer` 和 `EGLImage` 实现高效的外部图像导入（零拷贝）。
- 帧调度回调使用 `AndroidFrameCallback`（Choreographer 集成）。
- 帧时间戳使用 `EGL_ANDROID_get_frame_timestamps` 扩展获取合成器时间。
- 支持 Android API 级别差异化处理（API 19 最低要求）。

## 依赖关系

- `backend/platforms/PlatformEGLAndroid.h`, `backend/platforms/PlatformEGL.h`
- `ExternalStreamManagerAndroid.h` - SurfaceTexture 管理
- `AndroidNativeWindow.h`, `AndroidFrameCallback.h`, `AndroidSwapChainHelper.h`
- `android/native_window.h`, `android/hardware_buffer.h`
- `private/backend/VirtualMachineEnv.h` - JVM 环境
