# LightManager.h

## 文件概述

定义光源管理器，用于创建和管理场景中的光源（方向光、点光源、聚光灯），支持阴影、光通道等高级功能。

## 核心类/结构体

- **`LightManager`** - 光源管理器，继承自 `FilamentAPI`。使用 ECS 模式管理光源组件。
- **`LightManager::Type`** - 枚举：`SUN`/`DIRECTIONAL`/`POINT`/`FOCUSED_SPOT`/`SPOT`。
- **`LightManager::ShadowOptions`** - 阴影配置，包括阴影图大小、级联阴影、偏移等。
- **`LightManager::ShadowCascades`** - 级联阴影分割工具。
- **`LightManager::Builder`** - 光源组件构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder(type)` | 创建指定类型的光源 |
| `Builder::castShadows(enable)` | 启用/禁用阴影投射 |
| `Builder::position(pos)` / `direction(dir)` | 设置位置/方向 |
| `Builder::color(color)` / `intensity(value)` | 设置颜色/强度 |
| `Builder::falloff(radius)` | 设置衰减距离 |
| `Builder::spotLightCone(inner, outer)` | 设置聚光灯锥角 |
| `setPosition(i, pos)` / `setDirection(i, dir)` | 动态更新位置/方向 |
| `setIntensity(i, intensity)` | 动态更新强度 |
| `setShadowOptions(i, options)` | 设置阴影选项 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/Color.h`
- `utils/compiler.h`, `utils/Entity.h`, `utils/EntityInstance.h`

## 使用示例

```cpp
utils::Entity sun = utils::EntityManager::get().create();
LightManager::Builder(LightManager::Type::SUN)
    .castShadows(true)
    .direction({0, -1, -1})
    .intensity(100000.0f)
    .build(*engine, sun);
scene->addEntity(sun);
```
