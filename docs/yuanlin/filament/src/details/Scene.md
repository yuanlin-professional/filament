# Scene

## 文件概述

`FScene` 是 `Scene` 公共 API 的私有实现类，管理场景中的所有实体（可渲染物、光源）及其每帧数据。使用 Structure-of-Arrays（SoA）布局存储可渲染物和光源数据，为高效的裁剪和渲染提供缓存友好的数据访问。

## 核心类/结构体

### `FScene`
- 继承自 `Scene`
- `RenderableSoa` -- 可渲染物的 SoA 数据，包含 17 个字段：变换矩阵、可见性、蒙皮、变形、实例化、AABB、图元等
- `LightSoa` -- 光源的 SoA 数据，包含位置/半径、方向、阴影信息等 8 个字段
- 使用 `tsl::robin_set` 存储实体集合，支持 O(1) 的插入/删除

### `ShadowInfo`
- 每光源的阴影信息：是否投射阴影、接触阴影、阴影贴图索引

## 关键实现逻辑

- **prepare()** -- 每帧调用，从组件管理器收集可渲染物和光源数据，计算世界空间 AABB，支持阴影接收者即投射者的优化
- **prepareVisibleRenderables()** -- 裁剪后为可见物体准备 per-renderable UBO 数据
- **prepareDynamicLights()** -- 计算动态光源在屏幕空间的 Z 范围
- **方向光** -- 始终存储在 LightSoa 的第一个位置（`DIRECTIONAL_LIGHTS_COUNT = 1`）

## 依赖关系

- `Culler.h` -- 视锥体裁剪
- `components/RenderableManager.h`、`LightManager.h` -- 组件数据访问
- `ds/DescriptorSet.h` -- 描述符集管理
- `utils/StructureOfArrays.h` -- SoA 数据结构

## 与公共 API 的关系

直接对应 `filament::Scene`。通过 `Engine::createScene()` 创建，管理场景实体，设置天空盒和间接光照，绑定到 `View` 进行渲染。
