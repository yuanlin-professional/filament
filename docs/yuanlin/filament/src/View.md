# View.cpp

## 文件概述
View.cpp 实现了视图的公共 API。View 是 Filament 渲染管线的配置中心，定义了场景、相机、视口以及所有渲染选项（抗锯齿、后处理效果、阴影类型等）。

## 核心类/结构体/函数
- **View** - 视图公共接口
  - `setScene()` / `setCamera()` - 绑定场景和相机
  - `setViewport()` - 设置渲染视口
  - `setVisibleLayers()` / `getVisibleLayers()` - 图层可见性控制
  - `setFrustumCullingEnabled()` - 视锥裁剪开关
  - `setAntiAliasing()` / `setTemporalAntiAliasingOptions()` / `setMultiSampleAntiAliasingOptions()` - 抗锯齿配置
  - `setScreenSpaceReflectionsOptions()` - SSR 配置
  - `setColorGrading()` / `setDithering()` - 色彩分级和抖动
  - `setDynamicResolutionOptions()` - 动态分辨率
  - `setRenderQuality()` - 渲染质量
  - `setPostProcessingEnabled()` - 后处理开关
  - `setShadowingEnabled()` / `setShadowType()` / `setVsmShadowOptions()` / `setSoftShadowOptions()` - 阴影配置
  - `setAmbientOcclusion()` / `setAmbientOcclusionOptions()` - SSAO
  - `setBloomOptions()` / `setFogOptions()` / `setDepthOfFieldOptions()` / `setVignetteOptions()` - 后处理效果
  - `setBlendMode()` - 混合模式
  - `setRenderTarget()` - 离屏渲染目标
  - `setSampleCount()` - MSAA 采样数
  - `pick()` - 像素拾取查询
  - `setMaterialGlobal()` / `getMaterialGlobal()` - 全局材质参数
  - `setStereoscopicOptions()` - 立体渲染配置
  - `clearFrameHistory()` - 清除帧历史

## 关键实现逻辑
通过 `downcast` 委托给内部 `FView`。View 聚合了几乎所有渲染选项，每个选项的 set/get 方法成对出现。

## 依赖关系
- `details/View.h` - 内部实现
- `filament/View.h` - 公共头文件

## 被引用关系
- `Renderer` - `render(View*)` 使用 View 配置进行渲染
- 用户层代码通过 View 配置所有渲染参数
