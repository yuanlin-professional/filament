# FFilamentAsset（内部头文件）

## 文件概述
FFilamentAsset.h 是 FilamentAsset 的内部实现头文件，定义了完整的数据存储结构。它是 gltfio 中最核心的数据结构，几乎所有其他组件都依赖它。

## 核心结构体

- **`FFilamentAsset`**: 继承 FilamentAsset，包含所有实际存储：
  - 引擎和管理器指针：Engine, NameComponentManager, EntityManager, NodeManager, TrsTransformManager
  - 实体列表：mEntities（可渲染排前）、mLightEntities、mCameraEntities
  - GPU 资源：mVertexBuffers、mBufferObjects、mIndexBuffers、mMorphTargetBuffers
  - 蒙皮数据：mSkins（名称 + 反向绑定矩阵）
  - 纹理信息：mTextures（FixedCapacityVector<TextureInfo>）
  - 资源信息（variant）：ResourceInfo（标准）或 ResourceInfoExtended（扩展）
- **`ResourceInfo`**: 标准资源信息，包含 BufferSlot 列表和 primitive->VertexBuffer 映射。
- **`ResourceInfoExtended`**: 扩展资源信息，包含独立的 BufferSlot（含 data 指针和 targetData）以及 UriDataCache 句柄。
- **`TextureInfo`**: 纹理槽信息，包含绑定列表、Texture 指针、标志和所有权标记。
- **`SourceAsset`**: 引用计数的源数据，包含 cgltf 层次结构、Draco 缓存和 GLB 二进制数据。

## 关键设计

- 使用 `std::variant<ResourceInfo, ResourceInfoExtended>` 区分标准加载路径和扩展加载路径（mikktspace）。
- 使用 `std::shared_ptr<SourceAsset>` 实现源数据的共享所有权，允许异步操作在 asset 销毁后完成。
- GLTFIO_USE_FILESYSTEM 宏控制文件系统支持（Android/iOS/Web 禁用）。

## 依赖关系
- `gltfio/` 系列头文件
- `filament/` 系列头文件
- `DependencyGraph.h`, `DracoCache.h`, `FFilamentInstance.h`, `Utility.h`, `downcast.h`
- `cgltf.h`
