# View

## 文件概述

`FView` 是 `View` 公共 API 的私有实现类，是 Filament 渲染管线中最复杂的类之一。它将 Scene（场景）和 Camera（相机）绑定在一起，管理视口、渲染选项（抗锯齿、阴影、后处理、动态分辨率等）、Froxel 光照分簇、阴影管理和颜色 pass 描述符集等。

## 核心类/结构体

### `FView`
- 继承自 `View`
- 持有 `FScene*`、`FCamera*`、`FColorGrading*` 等核心对象引用
- `Froxelizer` -- 光照分簇系统，将视锥体划分为 froxel 进行光源裁剪
- `ShadowMapManager` -- 管理方向光和点光源的阴影贴图
- `ColorPassDescriptorSet` -- 颜色 pass 的描述符集管理
- `FrameHistory` -- 帧历史数据，用于时域抗锯齿（TAA）等算法
- `PIDController` -- 动态分辨率缩放的 PID 控制器

## 关键实现逻辑

- **渲染选项** -- 管理大量渲染选项：TAA、MSAA、FXAA、SSR、SSAO、Bloom、DOF、雾效、动态分辨率等
- **视口和裁剪** -- 管理逻辑视口、物理视口和裁剪矩形
- **Froxel 光照** -- 将视锥体划分为体素化网格，实现高效的多光源裁剪
- **阴影管理** -- 协调方向光级联阴影和点/聚光灯阴影的分配和渲染
- **立体渲染** -- 支持多视图渲染（Multiview）的相机和投影矩阵管理

## 依赖关系

- `Froxelizer.h` -- 光照分簇
- `ShadowMapManager.h` -- 阴影管理
- `ds/ColorPassDescriptorSet.h` -- 颜色 pass 描述符集
- `FrameHistory.h` -- 帧历史
- `details/Camera.h`、`Scene.h`、`ColorGrading.h`、`RenderTarget.h` -- 核心渲染对象

## 与公共 API 的关系

直接对应 `filament::View`。通过 `Engine::createView()` 创建，设置 Scene 和 Camera 后传递给 `Renderer::render()` 进行渲染。是配置渲染效果和质量的主要接口。
