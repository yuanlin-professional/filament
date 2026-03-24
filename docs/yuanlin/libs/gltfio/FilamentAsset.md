# FilamentAsset

## 文件概述
FilamentAsset 代表由 AssetLoader 创建的 Filament 对象集合，拥有从 glTF 资产加载的实体层次、顶点缓冲、索引缓冲、纹理等资源。它管理资源的生命周期，并提供按名称查询实体、获取包围盒、渐进式加载等功能。

## 核心类/结构体

- **`FilamentAsset`**: 公共接口，提供实体/灯光/相机/可渲染对象的访问，以及 `releaseSourceData()`、`getWireframe()` 等方法。
- **`FFilamentAsset`**: 内部实现结构体，包含所有实际的数据存储：
  - `mEntities`: 所有实体列表（可渲染实体排在前面）
  - `mTextures`: 纹理信息（FixedCapacityVector<TextureInfo>）
  - `mMeshCache`: 网格缓存，实现 VertexBuffer/IndexBuffer 在多节点引用同一 mesh 时的共享
  - `mDependencyGraph`: 依赖图，用于渐进式显示
  - `mResourceInfo`: 变体存储，区分标准资源信息和扩展资源信息
- **`TextureSlot`**: 纹理绑定槽，关联 MaterialInstance 与纹理参数名。
- **`Primitive`**: 网格基元，包含 VertexBuffer、IndexBuffer、AABB、UvMap 和变形目标缓冲。
- **`SourceAsset`**: 引用计数的源数据封装，包含 cgltf 层次结构、Draco 缓存和 GLB 数据。

## 关键实现逻辑

- **纹理绑定**: `addTextureBinding()` 检查纹理是否已创建，若已创建则直接调用 `setParameter()`，否则暂存绑定信息等待 ResourceLoader 创建纹理。
- **纹理采样器设置**: `applyTextureBinding()` 根据 glTF sampler 配置 Filament 的 wrap mode、min/mag filter。
- **资源释放**: `releaseSourceData()` 释放 CPU 端的源数据（纹理绑定列表、网格缓存、URI 列表、cgltf 数据），减少内存占用。
- **线框渲染**: `getWireframe()` 懒创建包围盒线框，用于调试诊断。
- **场景过滤**: `addEntitiesToScene()` 支持按 SceneMask 过滤，只将属于指定 glTF 场景的实体添加到 Filament Scene。

## 依赖关系
- `gltfio/NodeManager.h`, `gltfio/TrsTransformManager.h`, `gltfio/MaterialProvider.h`, `gltfio/TextureProvider.h`
- `DependencyGraph.h`, `DracoCache.h`, `FFilamentInstance.h`, `Utility.h`, `Wireframe.h`
- `filament/` 系列（Engine, Texture, VertexBuffer, IndexBuffer 等）
- `cgltf.h`
