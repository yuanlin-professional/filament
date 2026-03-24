# FTrsTransformManager（内部头文件）

## 文件概述
FTrsTransformManager.h 是 TrsTransformManager 的内部实现头文件，基于 SingleInstanceComponentManager 实现 TRS 分量的 SoA 存储。

## 核心类

- **`FTrsTransformManager`**: 继承 TrsTransformManager，使用 Sim 管理三个字段：
  - `TRANSLATION`: float3（平移）
  - `ROTATION`: quatf（旋转四元数）
  - `SCALE`: float3（缩放）

## 关键实现逻辑

- `create(Entity)` 使用默认值（零平移、单位旋转、单位缩放）。
- `create(Entity, translation, rotation, scale)` 创建并设置初始 TRS 值。
- `getTransform()` 调用 `composeMatrix()` 从 TRS 合成 mat4f。
- `setTrs()` 同时设置三个分量。
- 与 FNodeManager 使用相同的 Proxy/Field 模式实现 SoA 字段访问。

## 依赖关系
- `gltfio/TrsTransformManager.h`, `gltfio/math.h`
- `utils/SingleInstanceComponentManager.h`, `utils/Entity.h`
- `math/quat.h`
- `downcast.h`
