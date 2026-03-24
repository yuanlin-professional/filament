# Skybox

## 文件概述

`FSkybox` 是 `Skybox` 公共 API 的私有实现类，管理天空盒的渲染。天空盒通过一个 ECS 实体和专用材质实例实现，支持立方体贴图纹理或纯色两种模式，可配置图层遮罩和强度。

## 核心类/结构体

### `FSkybox`
- 继承自 `Skybox`
- 持有天空盒纹理 `mSkyboxTexture`（弱引用）和材质实例 `mSkyboxMaterialInstance`（拥有）
- 通过 `mSkybox` Entity 和 `FRenderableManager` 渲染
- 支持图层遮罩 `mLayerMask` 和光照强度 `mIntensity`

## 关键实现逻辑

- **createMaterial()** -- 静态方法，延迟创建天空盒专用材质（首次使用时由 `FEngine::getSkyboxMaterial()` 调用）
- **setColor()** -- 设置天空盒的纯色模式颜色
- **setLayerMask()** -- 控制天空盒在哪些渲染图层上可见

## 依赖关系

- `details/Engine.h` -- 引擎实例
- `private/backend/DriverApi.h` -- 驱动 API
- `utils/Entity.h` -- ECS 实体管理

## 与公共 API 的关系

直接对应 `filament::Skybox`。通过 `Skybox::Builder` 创建，设置到 `Scene` 上作为场景背景渲染。与 `IndirectLight` 配合提供完整的环境光照效果。
