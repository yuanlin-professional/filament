# SkinningBuffer

## 文件概述

`FSkinningBuffer` 是 `SkinningBuffer` 公共 API 的私有实现类，管理骨骼蒙皮动画所需的骨骼变换矩阵数据。骨骼数据存储在 GPU 缓冲区对象中，支持通过骨骼矩阵或骨骼结构体（四元数+平移）两种方式设置骨骼数据。

## 核心类/结构体

### `FSkinningBuffer`
- 继承自 `SkinningBuffer`
- 持有 `backend::Handle<backend::HwBufferObject>` 骨骼数据缓冲区句柄
- `mBoneCount` 记录骨骼数量
- `getPhysicalBoneCount()` 将骨骼数量向上对齐到 `CONFIG_MAX_BONE_COUNT` 的倍数

## 关键实现逻辑

- **setBones()** -- 支持 `RenderableManager::Bone`（四元数+平移）和 `mat4f` 两种输入格式
- **makeBone()** -- 将 4x4 矩阵转换为紧凑的 `BoneData` 格式（4x3 矩阵，去除最后一行 [0,0,0,1]）
- **索引和权重纹理** -- `createIndicesAndWeightsHandle()` 和 `setIndicesAndWeightsData()` 支持高级蒙皮（每顶点多于 4 个权重）

## 依赖关系

- `private/filament/UibStructs.h` -- `PerRenderableBoneUib` 骨骼 UBO 结构
- `private/filament/EngineEnums.h` -- `CONFIG_MAX_BONE_COUNT` 常量
- `details/Engine.h` -- 引擎驱动 API

## 与公共 API 的关系

直接对应 `filament::SkinningBuffer`。通过 `SkinningBuffer::Builder` 创建，结合 `RenderableManager::setSkinningBuffer()` 实现骨骼动画。
