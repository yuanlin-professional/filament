# Renderer

## 文件概述

`FRenderer` 是 `Renderer` 公共 API 的私有实现类，是 Filament 渲染管线的执行核心。负责帧的开始/结束、视图渲染、交换链管理、帧率控制、像素回读等操作。协调 FrameGraph、PostProcessManager 等子系统完成完整的渲染流程。

## 核心类/结构体

### `FRenderer`
- 继承自 `Renderer`
- 持有 `FSwapChain*` 当前交换链、`FrameSkipper` 帧跳过管理、`FrameInfoManager` 帧信息统计
- 支持多种 HDR/LDR 纹理格式选择
- `mCommandsHighWatermark` 跟踪渲染命令峰值使用量

## 关键实现逻辑

- **beginFrame/endFrame** -- 管理帧的生命周期，包括交换链切换、帧信息记录、GPU 同步
- **render()** -- 渲染单个 View，调用 `renderJob()` 执行实际渲染工作
- **renderJob()** -- 构建 FrameGraph，执行颜色/深度/阴影 pass，后处理（色调映射、抗锯齿、bloom 等）
- **帧率控制** -- 通过 `FrameRateOptions` 和帧时间历史实现自适应帧率
- **readPixels()** -- 支持从交换链或自定义 RenderTarget 回读像素数据
- **HDR 格式选择** -- 根据功能级别和平台能力选择 R11F_G11F_B10F、RGBA16F 等格式

## 依赖关系

- `details/Engine.h`、`details/View.h`、`details/Scene.h` -- 核心渲染对象
- `fg/FrameGraph.h` -- 帧图（FrameGraph）资源管理
- `PostProcessManager.h` -- 后处理管线
- `RenderPass.h` -- 渲染通道管理
- `TextureCache.h` -- 纹理资源缓存

## 与公共 API 的关系

直接对应 `filament::Renderer`。通过 `Engine::createRenderer()` 创建。典型使用流程：`beginFrame()` -> `render(view)` -> `endFrame()`。
