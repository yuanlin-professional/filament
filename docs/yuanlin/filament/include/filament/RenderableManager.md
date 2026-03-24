# RenderableManager.h

## 文件概述

定义可渲染对象管理器，负责创建和管理可被绘制的实体。每个可渲染对象由多个图元（Primitive）组成，每个图元有独立的几何和材质。

## 核心类/结构体

- **`RenderableManager`** - 可渲染对象管理器，继承自 `FilamentAPI`。使用 ECS 模式。
- **`RenderableManager::Builder`** - 构建器，配置几何、材质、阴影、蒙皮、变形等。
- **`RenderableManager::Bone`** - 骨骼变换（四元数+平移）。

## 主要 API (Builder)

| 方法 | 说明 |
|------|------|
| `geometry(index, type, vb, ib, ...)` | 设置图元几何数据 |
| `material(index, materialInstance)` | 绑定材质实例 |
| `boundingBox(aabb)` | 设置轴对齐包围盒 |
| `castShadows(enable)` / `receiveShadows(enable)` | 阴影投射/接收 |
| `skinning(buffer, count, offset)` | 启用 GPU 蒙皮 |
| `morphing(morphTargetBuffer)` | 启用变形目标 |
| `instances(count)` / `instances(count, instanceBuffer)` | 启用实例化 |
| `priority(p)` / `channel(c)` | 设置渲染优先级/通道 |
| `layerMask(select, values)` | 设置可见性层 |
| `build(engine, entity)` | 创建可渲染组件 |

## 主要 API (运行时)

| 方法 | 说明 |
|------|------|
| `setAxisAlignedBoundingBox(i, aabb)` | 更新包围盒 |
| `setMaterialInstanceAt(i, idx, mi)` | 更新材质 |
| `setMorphWeights(i, weights, count)` | 更新变形权重 |
| `setBones(i, transforms, count)` | 更新骨骼 |
| `computeAABB(vertices, indices, count)` | 静态工具：计算 AABB |

## 依赖关系

- `filament/Box.h`, `filament/FilamentAPI.h`, `filament/MaterialEnums.h`, `filament/MorphTargetBuffer.h`
- `backend/DriverEnums.h`, `utils/EntityInstance.h`
