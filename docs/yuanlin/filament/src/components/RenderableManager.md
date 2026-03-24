# RenderableManager

## 文件概述

`RenderableManager.h` / `RenderableManager.cpp` 实现了 Filament 引擎中可渲染对象组件的管理器。它是引擎中最复杂的组件管理器之一，负责管理网格几何数据、材质实例、蒙皮动画、变形目标、实例化渲染等功能。

## 核心类/结构体

- **`FRenderableManager`**: 核心管理器类，继承自公共 `RenderableManager`。
  - **`Visibility`**: 16 位位域结构，包含优先级(3位)、通道(3位)、投射/接收阴影、裁剪、雾效、几何类型等标志。
  - **`Skinning`**: 8 位位域，包含蒙皮启用标志和变形类型(3位)。
  - **`Bones`**: 16 字节结构，存储骨骼 UBO 句柄、骨骼数、偏移和蒙皮缓冲模式。
  - **`MorphWeights`**: 8 字节结构，存储变形权重 UBO 句柄和目标数。
  - **`InstancesInfo`**: 16 字节结构，存储实例缓冲区指针和实例计数。
  - **`Entry`**: Builder 使用的图元描述，包括顶点缓冲、索引缓冲、偏移、材质实例和混合顺序。
- **`Sim`**: SoA 管理器，包含 11 个组件字段（AABB、层掩码、变形权重、光通道、实例信息、可见性、蒙皮、图元、骨骼、变形目标缓冲、描述符集）。

## 关键实现逻辑

- **创建流程**: Builder 模式收集所有参数，`build()` 时进行大量前置条件检查（功能级别、骨骼数限制、AABB 非空等），然后通过 `FRenderableManager::create()` 分配 `FRenderPrimitive` 数组并初始化所有组件字段。
- **骨骼索引与权重处理 (`processBoneIndicesAndWights`)**: 支持高级蒙皮（每顶点超过 4 根骨骼），通过纹理存储额外的骨骼数据，前 4 个存储在顶点属性中，超出部分存储在特殊纹理中。
- **组件销毁**: 依次销毁 RenderPrimitive、描述符集、骨骼 UBO（非蒙皮缓冲模式下）和变形权重 UBO。
- **Adreno GPU 兼容**: 针对 Adreno GPU 的 uniform 数组崩溃问题，在骨骼数或变形目标数为零时进行初始化。

## 依赖关系

- `ds/DescriptorSet.h` - 每个可渲染对象的描述符集
- `details/Engine.h`, `details/VertexBuffer.h`, `details/IndexBuffer.h`, `details/Material.h` - 引擎核心组件
- `private/filament/UibStructs.h`, `private/filament/EngineEnums.h` - 内部 UBO 结构和枚举
- `HwRenderPrimitiveFactory.h` - 硬件渲染图元工厂
- `utils/SingleInstanceComponentManager.h` - ECS 基础
