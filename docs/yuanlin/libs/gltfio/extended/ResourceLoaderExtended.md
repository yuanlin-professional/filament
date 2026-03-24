# ResourceLoaderExtended

## 文件概述
ResourceLoaderExtended 是 ResourceLoader 的扩展实现，负责将 AssetLoaderExtended 在 CPU 端准备好的缓冲数据上传到 GPU。相比标准 ResourceLoader，它处理的数据已经过切线空间重网格化。

## 核心结构体

- **`ResourceLoaderExtended`**: 静态结构体，仅提供一个静态方法 `loadResources()`。

## 关键实现逻辑

- `loadResources()` 遍历所有 BufferSlot：
  - 对于顶点缓冲（`slot.vertices`）：创建 BufferObject，上传数据，绑定到 VertexBuffer。
  - 对于索引缓冲（`slot.indices`）：直接上传 BufferDescriptor 到 IndexBuffer。
  - 对于变形目标（`slot.target`）：分别上传位置数据和切线数据（TBN），然后释放 CPU 端内存。
- 所有数据通过 FREE_CALLBACK 在上传完成后自动释放。

## 依赖关系
- `FFilamentAsset.h`
- `backend/BufferDescriptor.h`
- `filament/BufferObject.h`
- `cgltf.h`
