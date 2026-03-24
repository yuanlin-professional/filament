# MorphTargetBuffer

## 文件概述

`FMorphTargetBuffer` 是 `MorphTargetBuffer` 公共 API 的私有实现类，管理变形目标（Morph Target/Blend Shape）的顶点位置和切线数据。数据存储在 GPU 纹理中，支持自定义变形模式。

## 核心类/结构体

### `FMorphTargetBuffer`
- 继承自 `MorphTargetBuffer`
- 持有位置纹理句柄 `mPbHandle` 和切线纹理句柄 `mTbHandle`
- `EmptyMorphTargetBuilder` -- 用于创建引擎内部虚拟变形缓冲区的特殊 Builder

### `MorphTargetBuffer::BuilderDetails`
- 顶点数 `mVertexCount`、变形目标数 `mCount`
- 可选位置/切线数据和自定义变形标志

## 关键实现逻辑

- **纹理存储** -- 变形目标数据存储为 GPU 纹理而非缓冲区，利用纹理采样在着色器中高效访问
- **setPositionsAt()** -- 支持 float3 和 float4 两种位置数据格式
- **setTangentsAt()** -- 使用 short4 归一化切线数据
- **updateDataAt()** -- 内部通用方法，将数据写入纹理的指定区域

## 依赖关系

- `details/Engine.h` -- 引擎驱动 API
- `private/filament/SibStructs.h` -- 采样器接口块结构
- `math/norm.h` -- 归一化工具

## 与公共 API 的关系

直接对应 `filament::MorphTargetBuffer`。通过 `MorphTargetBuffer::Builder` 创建，与 `RenderableManager` 配合实现面部表情等变形动画。
