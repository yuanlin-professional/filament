# View.h

## 文件概述

定义视图（View），包含渲染一个场景所需的全部状态：关联的 Scene、Camera、Viewport 及各种渲染参数和后处理选项。

## 核心类/结构体

- **`View`** - 视图对象，继承自 `FilamentAPI`。是一个重量级对象，内部缓存大量渲染数据。
- **`View::PickingQueryResult`** - 拾取查询结果（实体、深度、屏幕坐标）。
- **`View::PickingQuery`** - 拾取查询用户数据存储。

## 主要 API

| 方法 | 说明 |
|------|------|
| `setScene(scene)` / `getScene()` | 设置/获取关联的场景 |
| `setCamera(camera)` / `getCamera()` | 设置/获取关联的相机 |
| `setViewport(viewport)` | 设置渲染视口 |
| `setRenderTarget(rt)` | 设置离屏渲染目标 |
| `setColorGrading(cg)` | 设置颜色分级 |
| `setBlendMode(mode)` | 设置混合模式（OPAQUE/TRANSLUCENT） |
| `setVisibleLayers(select, values)` | 设置可见层 |
| `setShadowingEnabled(enable)` | 启用/禁用阴影 |
| `setShadowType(type)` | 设置阴影类型 |
| `setAntiAliasing(type)` | 设置后处理抗锯齿 |
| `setTemporalAntiAliasingOptions(opts)` | 设置 TAA 选项 |
| `setMultiSampleAntiAliasingOptions(opts)` | 设置 MSAA 选项 |
| `setDynamicResolutionOptions(opts)` | 设置动态分辨率选项 |
| `setBloomOptions(opts)` | 设置泛光选项 |
| `setFogOptions(opts)` | 设置雾效选项 |
| `setDepthOfFieldOptions(opts)` | 设置景深选项 |
| `setAmbientOcclusionOptions(opts)` | 设置 AO 选项 |
| `setPostProcessingEnabled(enable)` | 启用/禁用后处理 |
| `pick(x, y, functor, handler)` | 执行拾取查询 |
| `setMaterialGlobal(index, value)` | 设置材质全局变量 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/Options.h`
- `utils/compiler.h`, `utils/Entity.h`, `utils/FixedCapacityVector.h`
- `math/mathfwd.h`, `math/mat4.h`

## 使用示例

```cpp
View* view = engine->createView();
view->setScene(scene);
view->setCamera(camera);
view->setViewport({0, 0, width, height});
view->setAntiAliasing(AntiAliasing::FXAA);
view->setBloomOptions({.enabled = true, .strength = 0.1f});
```
