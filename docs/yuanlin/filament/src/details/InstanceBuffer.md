# InstanceBuffer

## 文件概述

`FInstanceBuffer` 是 `InstanceBuffer` 公共 API 的私有实现类，用于管理硬件实例化渲染所需的局部变换矩阵。每个实例可拥有独立的局部变换，在 `prepare()` 阶段与根变换矩阵组合后写入 per-renderable UBO。

## 核心类/结构体

### `FInstanceBuffer`
- 继承自 `InstanceBuffer`
- 使用 `FixedCapacityVector<mat4f>` 存储局部变换矩阵
- 实例数量上限为 `Engine::getMaxAutomaticInstances()`（即 `CONFIG_MAX_INSTANCES`）

### `InstanceBuffer::BuilderDetails`
- 实例数量 `mInstanceCount` 和可选的初始局部变换指针

## 关键实现逻辑

- **setLocalTransforms()** -- 批量设置指定偏移处的局部变换矩阵，带越界检查
- **prepare()** -- 核心方法，遍历每个实例，计算 `rootTransform * localTransform` 得到世界变换，同时计算法线矩阵并填充 per-renderable UBO 数据
- **法线矩阵计算** -- 使用 `mat3f::getTransformForNormals()` 和 `prescaleForNormals()` 确保非均匀缩放下法线正确

## 依赖关系

- `private/filament/UibStructs.h` -- PerRenderableData UBO 结构定义
- `details/Engine.h` -- 引擎实例

## 与公共 API 的关系

直接对应 `filament::InstanceBuffer`。通过 `InstanceBuffer::Builder` 创建，结合 `RenderableManager` 实现 GPU 实例化渲染。
