# FilamentInstance

## 文件概述
FilamentInstance 表示从 glTF 资产实例化出来的一组实体。每个实例拥有独立的实体层次、材质实例和动画器，但共享父 FilamentAsset 的顶点缓冲、索引缓冲和纹理。支持材质变体切换、蒙皮管理和包围盒重计算。

## 核心类/结构体

- **`FilamentInstance`**: 公共接口，提供实体访问、材质变体、动画器获取、蒙皮管理等方法。
- **`FFilamentInstance`**: 内部实现结构体，包含：
  - `mRoot`: 实例根实体
  - `mOwner`: 指向所属 FFilamentAsset 的弱引用
  - `mNodeMap`: glTF 节点到 Filament Entity 的映射（与 cgltf_node 数组大小相同，可稀疏填充）
  - `mSkins`: 每实例的蒙皮数据（关节列表和目标集合）
  - `mVariants`: 材质变体列表
  - `mMaterialInstances`: 材质实例列表
- **`VariantMapping`**: 将可渲染实体、基元索引与材质实例关联。
- **`Skin`**: 每实例蒙皮结构，包含关节实体列表和受影响的目标实体集合。

## 关键实现逻辑

- **包围盒重计算**: `recomputeBoundingBoxes()` 使用 JobSystem 并行计算所有基元的 AABB。对蒙皮网格，会考虑骨骼变换和权重。先临时断开根节点父子关系以消除自定义变换的影响。
- **材质变体应用**: `applyMaterialVariant()` 遍历变体映射表，通过 `RenderableManager::setMaterialInstanceAt()` 切换指定基元的材质。
- **蒙皮挂载/卸载**: `attachSkin()`/`detachSkin()` 动态添加或移除蒙皮影响的目标实体。
- **动画器创建**: `createAnimator()` 在资源加载完成后延迟创建，因为动画数据可能依赖外部缓冲区。

## 依赖关系
- `gltfio/FilamentInstance.h`, `gltfio/Animator.h`
- `FFilamentAsset.h`, `downcast.h`
- `filament/RenderableManager.h`, `filament/TransformManager.h`
- `utils/JobSystem.h`, `utils/FixedCapacityVector.h`
- `tsl/robin_set.h`
