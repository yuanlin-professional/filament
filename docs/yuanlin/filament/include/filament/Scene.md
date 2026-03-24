# Scene.h

## 文件概述

定义场景容器，用于管理可渲染对象和光源的平面列表。Scene 不是场景图，不提供层级关系。

## 核心类/结构体

- **`Scene`** - 场景容器，继承自 `FilamentAPI`。

## 主要 API

| 方法 | 说明 |
|------|------|
| `setSkybox(skybox)` | 设置天空盒 |
| `getSkybox()` | 获取天空盒 |
| `setIndirectLight(ibl)` | 设置间接光照 |
| `getIndirectLight()` | 获取间接光照 |
| `addEntity(entity)` | 添加实体到场景 |
| `addEntities(entities, count)` | 批量添加实体 |
| `remove(entity)` | 从场景移除实体 |
| `removeAllEntities()` | 移除所有实体 |
| `getEntityCount()` | 获取实体总数 |
| `getRenderableCount()` | 获取活跃可渲染对象数 |
| `getLightCount()` | 获取活跃光源数 |
| `hasEntity(entity)` | 检查实体是否在场景中 |
| `forEach(functor)` | 遍历场景中的所有实体 |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`, `utils/Invocable.h`

## 使用示例

```cpp
Scene* scene = engine->createScene();
scene->setSkybox(skybox);
scene->setIndirectLight(ibl);
scene->addEntity(renderable);
scene->addEntity(light);
```
