# FFilamentInstance（内部头文件）

## 文件概述
FFilamentInstance.h 是 FilamentInstance 的内部实现头文件，定义了实例级别的数据存储结构，包含实体列表、材质变体、蒙皮数据和节点映射。

## 核心结构体

- **`FFilamentInstance`**: 继承 FilamentInstance，包含：
  - `mRoot`: 实例根实体
  - `mOwner`: 指向所属 FFilamentAsset 的 const 指针
  - `mEntities`: 此实例的所有实体
  - `mVariants`: 材质变体列表（FixedCapacityVector<Variant>）
  - `mAnimator`: 此实例的动画器
  - `mSkins`: 每实例蒙皮数据（关节列表 + 目标实体集合）
  - `mNodeMap`: cgltf_node 索引到 Entity 的映射（可稀疏填充）
  - `mBoundingBox`: 实例级包围盒
  - `mMaterialInstances`: 此实例拥有的材质实例列表
- **`VariantMapping`**: 将可渲染实体、primitive 索引与材质实例关联。
- **`Variant`**: 命名的材质变体，包含映射列表。
- **`Skin`**: 每实例蒙皮，包含关节实体列表和目标实体集合（robin_set）。

## 关键设计

- `mNodeMap` 的大小等于原始资产中的 cgltf_node 数量，但可能稀疏填充（并非所有节点都在此实例的场景中）。
- 析构时销毁动画器和所有材质实例。

## 依赖关系
- `gltfio/FilamentInstance.h`
- `utils/CString.h`, `utils/Entity.h`, `utils/FixedCapacityVector.h`
- `math/mat4.h`
- `tsl/robin_set.h`
- `downcast.h`
