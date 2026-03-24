# ExternalStreamManagerAndroid.h / ExternalStreamManagerAndroid.cpp

## 文件概述

Android 平台的外部流管理器，封装 `SurfaceTexture` 的 JNI 操作，用于将 Android 视频帧/相机预览流作为 OpenGL 纹理使用。

## 核心类/结构体

### `ExternalStreamManagerAndroid`
管理 `SurfaceTexture` 的生命周期和 GL 上下文绑定。

**EGLStream**: 内部流结构体，持有 `jobject`（Java 全局引用）和 `ASurfaceTexture*`（NDK 原生指针）。

### 关键方法
- `acquire(jobject surfaceTexture)`: 获取 SurfaceTexture 的全局引用和原生指针。
- `release(Stream*)`: 释放资源。
- `attach(Stream*, intptr_t tname)`: 将 SurfaceTexture 附加到指定的 GL 纹理名称。
- `detach(Stream*)`: 从 GL 上下文分离。
- `updateTexImage(Stream*, int64_t*)`: 更新纹理图像并获取时间戳。
- `getTransformMatrix(Stream*)`: 获取纹理变换矩阵（mat4 -> mat3 转换）。

## 关键实现逻辑

- **双路 API**: Android 28+ 使用 NDK 的 `ASurfaceTexture_*` 系列函数，旧版本通过 JNI 反射调用 Java 方法。
- **JNI 缓存**: 首次使用时通过 `getEnvironmentSlow()` 查找并缓存所有 `SurfaceTexture` 方法 ID。
- **附加重试**: `attachToGLContext` 可能因 SurfaceTexture 已处于附加状态而失败（API < 26），此时先分离再重新附加。
- **变换矩阵转换**: 从 SurfaceTexture 获取的 4x4 矩阵提取为 3x3 矩阵（保留平移和缩放）。

## 依赖关系

- `private/backend/VirtualMachineEnv.h` - JVM 环境管理
- `backend/Platform.h` - Stream 类型
- `android/surface_texture.h` (API 28+)
- `jni.h` - JNI 接口
- `math/mat3.h`, `math/mat4.h`
