# Renderer.h

## 文件概述

定义渲染器，管理帧的提交和呈现流程。每个 Renderer 通常对应一个操作系统窗口，负责帧调度和渲染命令生成。

## 核心类/结构体

- **`Renderer`** - 渲染器，继承自 `FilamentAPI`。
- **`Renderer::DisplayInfo`** - 显示信息（刷新率等）。
- **`Renderer::FrameRateOptions`** - 帧率控制选项。
- **`Renderer::ClearOptions`** - 帧开始时的清除选项。
- **`Renderer::FrameInfo`** - 帧时序信息结构体。

## 主要 API

| 方法 | 说明 |
|------|------|
| `beginFrame(swapChain, vsyncTime)` | 开始一帧，返回是否应渲染 |
| `render(view)` | 渲染一个 View |
| `endFrame()` | 结束一帧并调度显示 |
| `renderStandaloneView(view)` | 独立渲染到 RenderTarget |
| `setDisplayInfo(info)` | 设置显示信息 |
| `setFrameRateOptions(options)` | 设置帧率选项 |
| `setClearOptions(options)` | 设置清除选项 |
| `readPixels(x, y, w, h, buffer)` | 读回帧缓冲像素 |
| `copyFrame(dstSwapChain, dst, src, flags)` | 复制帧到另一个交换链 |
| `getUserTime()` / `resetUserTime()` | 获取/重置用户时间 |
| `getFrameInfoHistory(size)` | 获取帧时序历史 |

## 依赖关系

- `filament/FilamentAPI.h`
- `utils/compiler.h`, `utils/FixedCapacityVector.h`
- `math/vec4.h`

## 使用示例

```cpp
Renderer* renderer = engine->createRenderer();
renderer->setClearOptions({.clearColor = {0,0,0,1}, .clear = true});

if (renderer->beginFrame(swapChain)) {
    renderer->render(view);
    renderer->endFrame();
}
```
