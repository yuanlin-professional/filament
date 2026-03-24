# Skybox.h

## 文件概述

定义天空盒，用于填充场景中未被几何体覆盖的像素。支持立方体贴图环境或纯色背景。

## 核心类/结构体

- **`Skybox`** - 天空盒对象，继承自 `FilamentAPI`。
- **`Skybox::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::environment(cubemap)` | 设置环境贴图（立方体贴图） |
| `Builder::showSun(show)` | 是否渲染太阳圆盘 |
| `Builder::intensity(value)` | 设置天空盒强度（无 IndirectLight 时使用） |
| `Builder::color(color)` | 设置纯色背景（无环境贴图时使用） |
| `Builder::priority(p)` | 设置渲染优先级（默认最低 7） |
| `Builder::build(engine)` | 创建 Skybox |
| `setColor(color)` | 动态更新颜色 |
| `setLayerMask(select, values)` | 设置可见性层 |
| `getIntensity()` | 获取强度 |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`, `math/mathfwd.h`

## 使用示例

```cpp
auto* skybox = Skybox::Builder()
    .environment(cubemapTexture)
    .showSun(true)
    .build(*engine);
scene->setSkybox(skybox);
```
