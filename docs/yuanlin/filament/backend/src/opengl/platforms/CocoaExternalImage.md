# CocoaExternalImage.h / CocoaExternalImage.mm

## 文件概述

macOS 平台的外部图像处理实现，将 CVPixelBuffer 转换为 OpenGL 纹理。由于 macOS 的 CoreVideo 返回的是 `GL_TEXTURE_RECTANGLE` 纹理，需要通过渲染通道将其转换为标准的 `GL_TEXTURE_2D`。

## 核心类/结构体

### `CocoaExternalImage`
继承 `OpenGLPlatform::ExternalTexture`。

**SharedGl**: 共享的 GL 资源（可跨多个实例复用）：
- 着色器程序：将 `sampler2DRect` 纹理绘制到 2D 纹理。
- 采样器对象：`GL_NEAREST` 过滤，`CLAMP_TO_EDGE` 环绕。

**State**: 保存/恢复 GL 状态（活跃纹理、纹理绑定、采样器、帧缓冲、视口等）。

### 关键方法
- `set(CVPixelBufferRef)`: 设置外部图像，锁定像素缓冲区，创建 CVOpenGLTexture，执行矩形到 2D 纹理的拷贝。
- `encodeCopyRectangleToTexture2D()`: 执行从 `GL_TEXTURE_RECTANGLE` 到 `GL_TEXTURE_2D` 的渲染通道转换。
- `getGlTexture()`: 返回转换后的 RGBA 2D 纹理 ID。

## 关键实现逻辑

- 使用 `CVOpenGLTextureCacheCreateTextureFromImage` 从 CVPixelBuffer 创建 GL 纹理。
- 渲染通道使用全屏三角形绘制，片段着色器从 `sampler2DRect` 采样并输出到 `GL_TEXTURE_2D`。
- 仅支持 `kCVPixelFormatType_32BGRA` 格式。
- 保存和恢复所有被修改的 GL 状态以避免干扰引擎的状态跟踪。

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `CoreVideo/CoreVideo.h` - CVPixelBuffer/CVOpenGLTextureCache
- `gl_headers.h`, `GLUtils.h`
