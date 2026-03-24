# ResourceLoader

## 文件概述
ResourceLoader 负责将 glTF 资产的顶点数据和纹理数据上传到 GPU。它支持同步加载（`loadResources`）和异步加载（`asyncBeginLoad`/`asyncUpdateLoad`），处理外部 URI 资源缓存、Draco/meshopt 解压、切线空间计算和纹理解码。

## 核心类/结构体

- **`ResourceConfiguration`**: 构造参数，包含 Engine 指针、glTF 文件路径（用于解析相对路径）、是否归一化蒙皮权重。
- **`ResourceLoader`**: 公共接口，提供 `addResourceData()`、`addTextureProvider()`、`loadResources()`、`asyncBeginLoad()` 等方法。
- **`ResourceLoader::Impl`**: Pimpl 实现，包含 URI 数据缓存、纹理提供器列表、纹理缓存（按缓冲指针和文件路径两种键）。

## 关键实现逻辑

- **缓冲上传**: `uploadBuffers()` 遍历所有 BufferSlot，为每个 accessor 创建 BufferObject 并上传到 GPU。处理索引缓冲的 uint8->uint16 转换、需要格式转换的顶点属性、以及变形目标数据。
- **Draco 解压**: 在加载资源前解压 KHR_draco_mesh_compression 扩展的网格。
- **meshopt 解压**: 调用 meshoptimizer 解码压缩的 buffer view。
- **切线计算**: `computeTangents()` 使用 JobSystem 并行生成表面方向四元数，处理基础网格和变形目标的切线。
- **纹理创建**: `createTextures()` 根据 MIME 类型委托给对应的 TextureProvider（stb/ktx2/webp），支持 data URI、BufferView、文件路径和用户缓存四种来源。
- **蒙皮数据**: `createSkins()` 复制反向绑定矩阵，可选地归一化蒙皮权重。
- **异步加载**: 通过 `asyncGetLoadProgress()` 报告进度（基于已推送/已弹出的纹理数量比）。

## 依赖关系
- `gltfio/TextureProvider.h`, `gltfio/ResourceLoader.h`
- `FFilamentAsset.h`, `TangentsJob.h`, `GltfEnums.h`, `Utility.h`
- `extended/ResourceLoaderExtended.h`
- `filament/` 系列（BufferObject, Texture, VertexBuffer, IndexBuffer, MorphTargetBuffer）
- `geometry/Transcoder.h`, `cgltf.h`, `meshoptimizer.h`
