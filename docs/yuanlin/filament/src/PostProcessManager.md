# PostProcessManager.h / PostProcessManager.cpp

## 文件概述
PostProcessManager 是 Filament 后处理管线的核心管理器，负责管理和执行所有后处理效果，包括 SSAO、SSR、Bloom、DOF、色彩分级、TAA、FXAA、雾效、动态分辨率缩放等。

## 核心类/结构体/函数
- **PostProcessManager** - 后处理管理器
  - `structure()` - 结构（深度）通道
  - `ssr()` - 屏幕空间反射通道
  - `screenSpaceAmbientOcclusion()` - SSAO
  - `dof()` - 景深效果
  - `bloom()` / `flarePass()` - 泛光和眩光
  - `colorGrading()` / `colorGradingSubpass()` - 色彩分级和色调映射
  - `fxaa()` - FXAA 抗锯齿
  - `taa()` / `TaaJitterCamera()` - 时间抗锯齿
  - `upscale()` / `upscaleFSR1()` / `upscaleSGSR1()` / `rcas()` - 动态分辨率上采样
  - `blit()` / `blitDepth()` / `resolve()` - 纹理复制和 MSAA 解析
  - `fogPrepare()` / `fog()` - 后处理雾效
  - `vsmMipmapPass()` / `gaussianBlurPass()` - VSM 阴影和高斯模糊
- **PostProcessMaterial** - 后处理材质的延迟加载包装器
  - `getMaterial()` - 首次访问时加载材质
- **ColorGradingConfig** / **StructurePassConfig** / **ScreenSpaceRefConfig** - 配置结构体

## 关键实现逻辑
- 所有后处理通过 FrameGraph 系统组织，每个效果作为 FrameGraph 节点
- PostProcessMaterial 使用联合体实现延迟加载：未加载时存储数据指针，加载后存储 FMaterial 指针
- 使用 MaterialInstanceManager 管理后处理材质实例的复用
- 支持子通道（subpass）模式的色彩分级，减少带宽消耗
- 抖动序列支持 RGSS4、Uniform Helix4 和 Halton32 三种模式

## 依赖关系
- `FrameGraph` 系统 - 资源管理和通道组织
- `MaterialInstanceManager` - 材质实例缓存
- 各种后处理材质着色器（fxaa、taa、bloom、colorGrading、dof、fog、fsr、sgsr、ssao 等）
- `ds/` 描述符集 - PostProcessDescriptorSet、SsrPassDescriptorSet、StructureDescriptorSet

## 被引用关系
- `FRenderer` / `FView` - 在渲染流程中调用后处理效果
- `RendererUtils` - 在颜色通道中使用后处理功能
