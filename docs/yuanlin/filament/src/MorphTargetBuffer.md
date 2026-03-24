# MorphTargetBuffer.cpp

## 文件概述
MorphTargetBuffer.cpp 实现了变形目标缓冲区的公共 API。变形目标（Morph Target / Blend Shape）用于面部动画、表情等场景，允许在多个预定义形状之间进行插值混合。

## 核心类/结构体/函数
- **MorphTargetBuffer** - 变形目标缓冲区公共接口
  - `setPositionsAt()` - 设置指定目标索引的顶点位置数据（支持 float3 和 float4 格式）
  - `setTangentsAt()` - 设置指定目标索引的切线数据（short4 格式）
  - `getVertexCount()` - 获取每个目标的顶点数量
  - `getCount()` - 获取变形目标数量
  - `hasPositions()` / `hasTangents()` - 查询是否包含位置/切线数据
  - `isCustomMorphingEnabled()` - 查询是否启用了自定义变形

## 关键实现逻辑
所有方法通过 `downcast` 委托给内部实现类 `FMorphTargetBuffer`。位置数据支持 float3（仅位置）和 float4（位置 + 额外分量）两种格式。

## 依赖关系
- `details/MorphTargetBuffer.h` - 内部实现
- `details/Engine.h` - 引擎

## 被引用关系
- `RenderableManager` - 通过 `getMorphTargetBuffer()` 获取变形目标缓冲区
- 用户层代码创建并填充变形目标数据
