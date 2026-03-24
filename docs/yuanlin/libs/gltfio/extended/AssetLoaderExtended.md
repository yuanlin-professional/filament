# AssetLoaderExtended

## 文件概述
AssetLoaderExtended 是 AssetLoader 的扩展实现，与标准实现的主要区别在于切线空间计算在此阶段（而非 ResourceLoader 阶段）完成。这是因为 TangentSpaceMesh 可能重新网格化输入数据，改变索引、顶点数和三角形数，必须在缓冲区上传到 GPU 之前解决。

## 核心类/结构体

- **`AssetLoaderExtended`**: 主结构体，提供 `createPrimitive()` 方法将 cgltf 数据转换为 Filament 的 VertexBuffer/IndexBuffer。
  - 持有 Engine 指针、glTF 路径、MaterialProvider 引用和 UriDataCache
  - 输入：cgltf_data、cgltf_primitive、名称、DracoCache、Material
  - 输出：Primitive（vertices、indices、aabb 等）和 BufferSlot 列表
- **`Attribute`**: cgltf 属性类型和索引的组合。
- **`FilamentAttribute`**: Filament 顶点属性类型和槽位的组合。

## 关键实现逻辑

- 在创建 primitive 时同时执行 Draco 解压、meshopt 解码和切线空间计算。
- 使用 TangentsJobExtended 计算切线，可能导致顶点/索引数据的重排列。
- 将计算结果存储在 CPU 内存的 BufferSlot 中，由 ResourceLoaderExtended 后续上传到 GPU。
- 仅支持桌面平台（不支持 Android/iOS/Web）。

## 依赖关系
- `FFilamentAsset.h`, `Utility.h`, `DracoCache.h`, `GltfEnums.h`
- `TangentsJobExtended.h`
- `gltfio/AssetLoader.h`, `gltfio/MaterialProvider.h`
- `filament/BufferObject.h`
- `cgltf.h`
