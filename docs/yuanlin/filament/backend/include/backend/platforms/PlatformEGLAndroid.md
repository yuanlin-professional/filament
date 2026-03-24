# PlatformEGLAndroid.h

## 文件概述

定义了 `PlatformEGLAndroid` 类，是 Android 平台上 EGL + OpenGL ES 的具体实现。在 `PlatformEGL` 基础上添加了 Android 特有功能：硬件缓冲区外部图像、视频流、帧时序查询和性能提示管理。

## 核心类

### PlatformEGLAndroid (继承自 PlatformEGL, AndroidNdk)

**内部类型**:
- `ExternalImageDescAndroid` - Android 外部图像描述（宽高/格式/用途）
- `ExternalImageEGLAndroid` - Android 外部图像（AHardwareBuffer + EGLImage）
- `SyncEGLAndroid` - Android EGL 同步对象

**Android 特有 EGL 扩展**:
- `ANDROID_presentation_time` - 呈现时间控制
- `ANDROID_get_frame_timestamps` - 帧时间戳查询
- `ANDROID_native_fence_sync` - 原生栅栏同步

## 主要 API

| 方法 | 说明 |
|------|------|
| `createExternalImage(buffer, sRGB)` | 从 AHardwareBuffer 创建外部图像 |
| `getExternalImageDesc(handle)` | 获取外部图像描述信息 |
| `convertSyncToFd(sync, fd)` | 将同步对象转为文件描述符 |
| `getOSVersion()` | 返回 Android SDK 版本 |
| `isCompositorTimingSupported()` | 合成器时序支持 |
| `queryCompositorTiming(...)` / `queryFrameTimestamps(...)` | 时序查询 |
| `setPresentationTime(ns)` | 设置帧呈现时间 |
| Stream 系列方法 | 外部视频流创建/绑定/更新 |

## 依赖关系

- `backend/platforms/PlatformEGL.h` - 基类
- `backend/platforms/AndroidNdk.h` - NDK 封装
- `backend/AcquiredImage.h` / `backend/DriverEnums.h` / `backend/Platform.h`
- `utils/android/PerformanceHintManager.h` - 性能提示
