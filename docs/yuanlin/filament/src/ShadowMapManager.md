# ShadowMapManager.h / ShadowMapManager.cpp

## 文件概述
ShadowMapManager 是 Filament 阴影系统的顶层管理器，负责分配和管理所有阴影贴图、计算阴影参数、构建阴影渲染通道，并提供阴影 Uniform 数据。

## 核心类/结构体/函数
- **ShadowMapManager** - 阴影贴图管理器
  - **ShadowTechnique** 枚举 - NONE / SHADOW_MAP / SCREEN_SPACE
  - **Builder** - 构建器，收集方向光和聚光灯阴影贴图配置
    - `directionalShadowMap()` / `shadowMap()` - 添加阴影贴图
    - `hasShadowMaps()` - 检查是否有阴影贴图需要渲染
- **ShadowMappingUniforms** - 阴影映射 Uniform 数据
  - `cascadeSplits` - 级联分割距离
  - `ssContactShadowDistance` - 屏幕空间接触阴影距离
  - `directionalShadows` / `cascades` - 方向光阴影和级联数
  - `atlasResolution` - Atlas 分辨率

## 关键实现逻辑
- 支持级联阴影贴图（CSM）用于方向光，多个级联共享 Atlas 纹理
- 使用 AtlasAllocator 分配阴影贴图在 Atlas 中的位置
- Builder 模式收集所有需要阴影贴图的光源，然后统一分配和渲染
- 支持 PCF 和 VSM 两种阴影技术的切换

## 依赖关系
- `ShadowMap.h` / `AtlasAllocator.h` - 单个阴影贴图和 Atlas 分配
- `RenderPass.h` - 渲染通道
- `fg/FrameGraph*.h` - FrameGraph 系统
- `details/Engine.h` / `details/Scene.h` - 引擎和场景

## 被引用关系
- `FView` - 持有 ShadowMapManager，在每帧中更新阴影
- `PostProcessManager` - 调试阴影级联可视化
