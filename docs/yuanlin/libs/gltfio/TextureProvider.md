# TextureProvider

## 文件概述
TextureProvider 定义了纹理解码的异步接口，允许客户端实现自己的 JPEG/PNG/KTX2/WebP 解码设施。它同步创建 Filament Texture 对象（确定尺寸），异步填充 miplevel 数据。gltfio 提供了三个内置实现。

## 核心类/结构体

- **`TextureProvider`**: 抽象基类，定义推拉队列模型：
  - `pushTexture()`: 创建纹理并推入解码队列
  - `popTexture()`: 弹出已解码完成的纹理
  - `updateQueue()`: 轮询解码进度，上传 mipmap 图像
  - `waitForCompletion()` / `cancelDecoding()`: 等待/取消解码
  - `getPushedCount()` / `getPoppedCount()` / `getDecodedCount()`: 统计计数
- **`TextureFlags`**: 位标志枚举，标记纹理是否为 sRGB 色彩空间。

## 工厂函数

- **`createStbProvider()`**: 基于 stb_image 的解码器，支持 image/png 和 image/jpeg。
- **`createKtx2Provider()`**: KTX2 格式解码器，支持 KHR_texture_basisu 规范。
- **`createWebpProvider()`**: WebP 格式解码器（需编译时启用 webp 支持）。
- **`isWebpSupported()`**: 查询编译时是否包含 webp 支持。

## 关键实现逻辑

各提供器遵循相同的异步模式：pushTexture 时同步读取图像头以确定尺寸并创建 Texture 对象，然后在后台线程进行解码。updateQueue 时将解码完成的数据通过 Texture::setImage() 上传到 GPU。

## 依赖关系
- `filament/Engine.h`, `filament/Texture.h`
- `utils/compiler.h`, `utils/BitmaskEnum.h`
