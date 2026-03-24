# SibGenerator.h / SibGenerator.cpp

## 文件概述
采样器接口块（SIB）生成器，为引擎内部使用的采样器组（每视图、每可渲染对象）生成 `SamplerInterfaceBlock` 实例。

## 核心类/结构体/函数
- **`SibGenerator`** - 静态工具类
  - `getPerViewSib(Variant)` - 获取每视图的采样器接口块（阴影贴图、IBL、SSAO、SSR、雾等）
  - `getPerRenderableSib(Variant)` - 获取每可渲染对象的采样器接口块（变形目标位置/法线、骨骼）
  - `getSib(DescriptorSetBindingPoints, Variant)` - 通用获取方法

## 关键实现逻辑
根据变体类型返回不同配置的采样器块。例如深度变体仅需少量采样器，SSR 变体需要额外的历史缓冲区采样器。

## 依赖关系
- `private/filament/Variant.h`、`private/filament/EngineEnums.h`
