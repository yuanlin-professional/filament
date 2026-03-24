# AssetLoader

## 文件概述
AssetLoader 是 gltfio 的核心加载器，负责消费 glTF 2.0 内容（JSON 或 GLB）并生成 FilamentAsset 对象。它创建 Filament 的 VertexBuffer、IndexBuffer、Renderable、Light、Camera 等组件，并管理材质实例的缓存。

## 核心类/结构体

- **`AssetConfiguration`**: 构造参数，包含 Engine 指针、MaterialProvider、NameComponentManager 等。
- **`AssetConfigurationExtended`**: 扩展配置，用于启用 mikktspace 切线空间计算。
- **`AssetLoader`**: 公共接口，提供 `create()`/`destroy()`/`createAsset()`/`createInstancedAsset()` 等静态和实例方法。
- **`FAssetLoader`**: 内部实现类，继承自 AssetLoader，包含实际的加载逻辑。
- **`MaterialInstanceCache`**: 材质实例缓存，避免同一 glTF 材质被重复创建。相同 glTF 材质引用的多个 primitive 共享同一个 MaterialInstance。

## 关键实现逻辑

- **两阶段加载**: 第一阶段 `createRootAsset()` 遍历场景节点创建 VertexBuffer/IndexBuffer；第二阶段 `createInstances()` 为每个实例创建实体层次、Renderable 组件和材质实例。
- **实例化**: `createInstancedAsset()` 支持创建多个共享顶点/索引缓冲但拥有独立实体层次和材质实例的实例。
- **材质键生成**: `getMaterialKey()` 从 cgltf_material 提取所有 PBR 属性（baseColor、metallic-roughness、clearcoat、transmission、sheen、volume、specular 等），生成用于缓存查找的 MaterialKey。
- **材质参数设置**: `createMaterialInstance()` 设置所有材质参数（颜色因子、纹理绑定、UV 变换矩阵等），支持 PBR Metallic-Roughness 和 Specular-Glossiness 两种工作流。
- **灯光/相机创建**: 支持方向光、点光、聚光灯以及透视/正交相机。

## 依赖关系
- `gltfio/Animator.h`, `gltfio/MaterialProvider.h`, `gltfio/math.h`
- `FFilamentAsset.h`, `FNodeManager.h`, `FTrsTransformManager.h`, `GltfEnums.h`, `Utility.h`
- `extended/AssetLoaderExtended.h`
- `filament/` 系列（Engine, Material, VertexBuffer, IndexBuffer, LightManager 等）
- `cgltf.h`, `tsl/robin_map.h`
