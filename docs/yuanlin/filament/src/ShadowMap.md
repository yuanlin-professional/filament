# ShadowMap.h / ShadowMap.cpp

## 文件概述
ShadowMap 实现了单个阴影贴图的计算和管理，支持方向光、聚光灯和点光源三种阴影类型。它负责计算光源空间的投影矩阵、裁剪和分辨率优化。

## 核心类/结构体/函数
- **ShadowMap** - 阴影贴图类
  - **ShadowType** 枚举 - DIRECTIONAL / SPOT / POINT
  - **ShadowMapInfo** - 阴影贴图信息（atlas 尺寸、纹理尺寸、是否 VSM 等）
  - **SceneInfo** - 场景信息（投射者/接收者近远平面、世界空间体积等）
  - `terminate()` - 释放资源
- **可见性掩码常量**：
  - `VISIBLE_RENDERABLE` - 相机可见
  - `VISIBLE_DIR_SHADOW_RENDERABLE` - 方向光阴影可见
  - `VISIBLE_DYN_SHADOW_RENDERABLE` - 动态光源阴影可见

## 关键实现逻辑
- 阴影贴图使用 Atlas 方式组织，多个阴影贴图共享一张大纹理
- 针对 PCF 和 VSM 两种阴影技术，"阴影可渲染对象"的定义不同：PCF 仅包含投射者，VSM 包含投射者和接收者
- `ShadowMapInfo` 中 `shadowDimension = textureDimension - 2`，预留 1 像素边框
- SceneInfo 跟踪光源空间中投射者和接收者的近远平面，用于优化投影矩阵

## 依赖关系
- `Culler.h` - 视锥裁剪
- `ds/ShadowMapDescriptorSet.h` - 阴影贴图描述符集
- `details/Camera.h` / `details/Scene.h` - 相机和场景
- `components/LightManager.h` - 光源数据

## 被引用关系
- `ShadowMapManager` - 管理所有 ShadowMap 实例
- `RenderPass` - 引用 ShadowMap 中的可见性常量
