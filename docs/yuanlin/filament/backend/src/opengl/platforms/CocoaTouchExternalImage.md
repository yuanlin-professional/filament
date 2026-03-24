# CocoaTouchExternalImage.h / CocoaTouchExternalImage.mm

## 文件概述

iOS 平台的外部图像处理实现，将 CVPixelBuffer 转换为 OpenGL ES 纹理。支持 BGRA 和 YCbCr（420f 双平面）两种像素格式，后者需要通过渲染通道进行色彩空间转换。

## 核心类/结构体

### `CocoaTouchExternalImage`
继承 `OpenGLPlatform::ExternalTexture`。

**SharedGl**: 共享 GL 资源：
- YCbCr 到 RGB 转换的着色器程序。
- 片段着色器使用 BT.601 色彩矩阵进行 YCbCr -> RGB 转换。

**State**: 保存/恢复 GL 状态。

### 关键方法
- `set(CVPixelBufferRef)`: 根据格式类型分发处理：
  - BGRA（单平面）: 直接创建纹理。
  - YCbCr（双平面）: 分别创建亮度和色度纹理，执行颜色转换渲染通道。
- `encodeColorConversionPass()`: 使用全屏三角形渲染，从两个输入纹理（Y 平面和 CbCr 平面）生成 RGB 纹理。

## 关键实现逻辑

- 使用 `CVOpenGLESTextureCacheCreateTextureFromImage` 从 CVPixelBuffer 创建 GLES 纹理。
- YCbCr 转换使用 BT.601 全范围色彩矩阵（硬编码在片段着色器中）。
- 对于 BGRA 格式直接返回 CVOpenGLESTexture 的纹理 ID，无需额外渲染通道。
- 双平面 YCbCr 图像在转换后立即释放源 CVPixelBuffer 和临时纹理。

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `CoreVideo/CoreVideo.h` - CVPixelBuffer/CVOpenGLESTextureCache
- `OpenGLES/EAGL.h`, `OpenGLES/ES3/gl.h`
- `GLUtils.h`, `math/vec2.h`
