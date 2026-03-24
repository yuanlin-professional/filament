# PlatformCocoaTouchGL.mm

## 文件概述

iOS 平台的 OpenGL ES 上下文管理实现，使用 EAGLContext 和 CAEAGLLayer 创建和管理 GLES 上下文和交换链。

## 核心类/结构体

### `PlatformCocoaTouchGLImpl`
平台内部实现：
- `mGLContext`: EAGLContext（GLES 3.0）。
- `mDefaultFramebuffer/Colorbuffer/Depthbuffer`: 默认帧缓冲及其附件。
- `mTextureCache`: CVOpenGLESTextureCache。
- `mExternalImageSharedGl`: YCbCr 转换的共享 GL 资源。

### 关键方法
- `createDriver()`: 创建 EAGLContext，设置默认帧缓冲（颜色+深度渲染缓冲区），创建纹理缓存。
- `makeCurrent()`: 切换 CAEAGLLayer，当图层变化或尺寸改变时重新绑定颜色/深度渲染缓冲区。
- `commit()`: 调用 `presentRenderbuffer` 提交帧。
- `getDefaultFramebufferObject()`: 返回自定义的默认帧缓冲 ID。

## 关键实现逻辑

- iOS 上 OpenGL ES 没有默认帧缓冲，需要手动创建 FBO 并将 CAEAGLLayer 绑定为颜色渲染缓冲区的存储。
- 交换链尺寸变化时通过 `renderbufferStorage:fromDrawable:` 重新分配颜色缓冲区，并同步调整深度缓冲区。
- 外部图像支持 BGRA 和 YCbCr 两种格式。

## 依赖关系

- `backend/platforms/PlatformCocoaTouchGL.h`
- `CocoaTouchExternalImage.h` - 外部图像处理
- `OpenGLES/EAGL.h`, `UIKit/UIKit.h`
- `CoreVideo/CoreVideo.h`
