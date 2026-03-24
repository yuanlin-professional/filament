# WebpProvider

## 文件概述
WebpProvider 是 TextureProvider 的 WebP 格式实现，支持有损和无损 WebP 图像的解码。此功能需要编译时启用 webp 支持（FILAMENT_SUPPORTS_WEBP_TEXTURES 宏）。

## 核心类

- **`WebpProvider`**: 实现 TextureProvider 接口（条件编译）。
  - 使用 libwebp 进行解码
  - 与 StbProvider 采用相同的异步队列模式

## 关键实现逻辑

- **条件编译**: 若 `FILAMENT_SUPPORTS_WEBP_TEXTURES` 未定义，`createWebpProvider()` 返回 nullptr，`isWebpSupported()` 返回 false。
- 启用时，`pushTexture()` 使用 `WebPGetInfo()` 读取图像尺寸，创建 Texture 对象，后台调用 `WebPDecode*()` 解码像素数据。
- 与其他 Provider 共享相同的推拉队列接口模式。

## 依赖关系
- `gltfio/TextureProvider.h`
- `filament/Engine.h`, `filament/Texture.h`
- `utils/JobSystem.h`, `utils/Log.h`
- `webp/decode.h`（仅在 FILAMENT_SUPPORTS_WEBP_TEXTURES 时）
