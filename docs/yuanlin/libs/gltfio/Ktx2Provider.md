# Ktx2Provider

## 文件概述
Ktx2Provider 是 TextureProvider 的 KTX2 格式实现，支持 KHR_texture_basisu 规范定义的 Basis Universal 压缩纹理。使用后台线程进行转码，前台线程上传数据到 GPU。

## 核心类

- **`Ktx2Provider`**: 实现 TextureProvider 接口，内部管理转码队列。
  - 状态机：TRANSCODING -> READY -> POPPED
  - 使用 `ktxreader::Ktx2Reader` 进行 Basis Universal 转码
  - 支持多种 GPU 压缩格式（ETC、ASTC、BC 等）

## 关键实现逻辑

- `pushTexture()` 解析 KTX2 文件头确定纹理属性，选择目标 GPU 压缩格式，创建 Texture 对象，然后启动后台转码作业。
- `updateQueue()` 检查已完成的转码作业，上传压缩纹理数据。
- KTX2 格式天然支持 mipmap，无需额外生成。

## 依赖关系
- `gltfio/TextureProvider.h`
- `filament/Engine.h`, `filament/Texture.h`
- `utils/JobSystem.h`
- `ktxreader/Ktx2Reader.h`
