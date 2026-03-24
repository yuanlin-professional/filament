# MetalExternalImage

## 文件概述

MetalExternalImage 处理外部图像（CVPixelBuffer）到 Metal 纹理的转换。支持 BGRA 格式的直接映射和 YCbCr（420f 双平面）格式的 GPU 色彩空间转换。常用于相机预览帧、视频帧等外部图像源的集成。

**源文件**: `MetalExternalImage.h`, `MetalExternalImage.mm`

## 核心类/结构体

- **`MetalExternalImage`** - 外部图像封装类
  - 持有 `CVPixelBufferRef`（原始图像）、`CVMetalTextureRef`（Metal 纹理引用）或 `id<MTLTexture>`（转换后的 RGB 纹理）
  - `createFromImage()` - 从完整 CVPixelBuffer 创建（自动处理 BGRA 和 YCbCr）
  - `createFromImagePlane()` - 从 CVPixelBuffer 的指定平面创建
  - `getMtlTexture()` - 获取可用于着色器采样的 Metal 纹理
  - 支持移动语义，禁用拷贝

## 关键实现逻辑

- **BGRA 直接映射**: 单平面 BGRA 图像通过 `CVMetalTextureCacheCreateTextureFromImage` 零拷贝映射为 Metal 纹理
- **YCbCr 色彩转换**: 双平面 420f 图像通过 compute shader 执行 YCbCr -> RGB 转换：
  - Y 平面（R8Unorm）和 CbCr 平面（RG8Unorm）分别映射为 Metal 纹理
  - 使用内嵌 MSL compute kernel 执行 BT.601 色彩矩阵转换
  - 输出到 RGBA8Unorm 纹理，线程组大小 16x16
  - 转换完成后通过命令缓冲区完成回调释放输入纹理引用
- **生命周期管理**: 通过 `CVPixelBufferRetain/Release` 管理 CVPixelBuffer 引用计数，析构时自动释放
- **Compute Pipeline 缓存**: `ensureComputePipelineState()` 惰性创建计算管线状态，缓存在 `MetalContext::externalImageComputePipelineState`

## 依赖关系

- `MetalContext.h` - 访问设备、命令队列、纹理缓存、计算管线状态
- `MetalEnums.h` - 像素格式转换
- `MetalUtils.h` - 纹理工具
- `CoreVideo/CoreVideo.h` - CVPixelBuffer、CVMetalTexture API
- `Metal/Metal.h` - Metal 计算管线
