# MorphTargetBuffer.h

## 文件概述

定义变形目标缓冲区，用于存储顶点变形动画数据（位置和切线），支持自动和手动变形两种模式。

## 核心类/结构体

- **`MorphTargetBuffer`** - 变形目标缓冲区，继承自 `FilamentAPI`。
- **`MorphTargetBuffer::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::vertexCount(count)` | 设置顶点数 |
| `Builder::count(count)` | 设置变形目标数 |
| `Builder::withPositions(enable)` | 启用内置位置变形 |
| `Builder::withTangents(enable)` | 启用内置切线变形 |
| `Builder::enableCustomMorphing(enable)` | 启用自定义变形管线 |
| `Builder::build(engine)` | 创建 MorphTargetBuffer |
| `setPositionsAt(engine, target, positions, count, offset)` | 更新指定目标的位置数据 |
| `setTangentsAt(engine, target, tangents, count, offset)` | 更新指定目标的切线数据 |
| `getVertexCount()` / `getCount()` | 获取顶点数/目标数 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/Engine.h`
- `utils/compiler.h`, `utils/StaticString.h`, `math/mathfwd.h`
