# RendererUtils.h / RendererUtils.cpp

## 文件概述
RendererUtils 提供渲染流程中的工具函数，主要包含颜色通道和折射通道的 FrameGraph 构建逻辑，以及像素回读功能。

## 核心类/结构体/函数
- **RendererUtils** - 渲染工具类（全部静态方法）
  - `colorPass()` - 构建颜色渲染通道的 FrameGraph 节点
  - `refractionPass()` - 构建折射渲染通道，处理屏幕空间折射
  - `readPixels()` - 从渲染目标回读像素数据
  - `getFirstRefractionCommand()` - 获取折射命令的起始位置
- **ColorPassConfig** - 颜色通道配置
  - `physicalViewport` / `logicalViewport` - 物理/逻辑视口
  - `scale` - 动态分辨率缩放因子
  - `hdrFormat` - HDR 格式
  - `msaa` / `clearFlags` / `clearColor` - MSAA 和清除参数
  - `hasContactShadows` / `hasScreenSpaceReflectionsOrRefractions` - 特性开关
  - `fogAsPostProcess` - 是否使用后处理雾效
- **ColorPassInput** / **ColorPassOutput** - 通道输入/输出资源句柄

## 关键实现逻辑
- `colorPass()` 在 FrameGraph 中创建颜色通道节点，配置颜色/深度/模板缓冲区、SSAO、SSR 等资源
- `refractionPass()` 将渲染命令拆分为不透明和折射两部分，分别渲染后合成
- 支持色彩分级作为子通道（subpass）执行，减少带宽消耗

## 依赖关系
- `PostProcessManager.h` / `RenderPass.h` - 后处理和渲染通道
- `fg/FrameGraph*.h` - FrameGraph 系统
- `details/Engine.h` / `details/View.h` - 引擎和视图

## 被引用关系
- `FRenderer` - 在渲染流程中调用 colorPass 和 refractionPass
