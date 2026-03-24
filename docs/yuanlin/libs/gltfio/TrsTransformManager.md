# TrsTransformManager

## 文件概述
TrsTransformManager 是一个 ECS 组件管理器，用于存储 glTF 节点的 TRS（Translation/Rotation/Scale）分解信息。该组件专为动画系统设计，不用于通用变换，Animator 通过它来独立设置和获取 TRS 分量。

## 核心类/结构体

- **`TrsTransformManager`**: 公共接口，提供 `create()`、`destroy()` 和 TRS 各分量的存取方法，以及 `getTransform()` 合成 4x4 矩阵。
- **`FTrsTransformManager`**: 内部实现，基于 `SingleInstanceComponentManager` SoA 存储三个字段：
  - `TRANSLATION`: float3（平移向量）
  - `ROTATION`: quatf（旋转四元数）
  - `SCALE`: float3（缩放向量）

## 关键实现逻辑

- `getTransform()` 调用 `composeMatrix()` 从 TRS 分量合成 4x4 变换矩阵。
- `create(Entity, translation, rotation, scale)` 在创建组件时立即设置初始 TRS 值。
- 默认构造使用 translation={0,0,0}、rotation=identity、scale={1,1,1}。
- 与 Filament 的 TransformManager 互补：TransformManager 存储矩阵形式，TrsTransformManager 存储分解形式，动画时先修改 TRS 再合成矩阵。

## 依赖关系
- `filament/FilamentAPI.h`
- `utils/SingleInstanceComponentManager.h`, `utils/Entity.h`
- `math/quat.h`, `math/vec3.h`, `math/mat4.h`
- `gltfio/math.h`（composeMatrix 函数）
- `downcast.h`
