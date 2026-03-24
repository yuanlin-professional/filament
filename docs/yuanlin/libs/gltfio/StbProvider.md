# StbProvider

## 文件概述
StbProvider 是 TextureProvider 的 stb_image 实现，负责解码 image/png 和 image/jpeg 格式的纹理。使用后台线程进行解码，前台线程通过 updateQueue 上传 mipmap 数据到 GPU。

## 核心类

- **`StbProvider`**: 实现 TextureProvider 接口，内部管理一个纹理解码队列。
  - 状态机：DECODING -> READY -> POPPED
  - 使用 JobSystem 在后台线程执行 stb_image 解码
  - 同步创建 Filament Texture 对象（确定纹理尺寸和格式）
  - 异步填充 miplevel 数据

## 关键实现逻辑

- `pushTexture()` 先用 stb_image 读取图像头信息获取宽高，创建 Texture 对象，然后启动后台解码作业。
- `updateQueue()` 检查已完成的解码作业，调用 `Texture::setImage()` 上传数据并可选地生成 mipmap。
- `popTexture()` 返回已完全上传的纹理。
- 支持 sRGB 标志，在创建纹理时选择正确的内部格式。

## 依赖关系
- `gltfio/TextureProvider.h`
- `filament/Engine.h`, `filament/Texture.h`
- `utils/JobSystem.h`, `utils/Log.h`
- `stb_image.h`
