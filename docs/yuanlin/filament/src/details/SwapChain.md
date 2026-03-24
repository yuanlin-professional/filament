# SwapChain

## 文件概述

`FSwapChain` 是 `SwapChain` 公共 API 的私有实现类，封装了与操作系统窗口系统对接的交换链。支持原生窗口和无窗口（headless）两种创建模式，提供丰富的配置标志（透明、可读、受保护内容、模板缓冲等）。

## 核心类/结构体

### `FSwapChain`
- 继承自 `SwapChain`
- 持有 `backend::Handle<backend::HwSwapChain>` 后端交换链句柄
- 支持帧调度回调 `FrameScheduledCallback` 和帧完成回调
- 配置标志：`CONFIG_TRANSPARENT`、`CONFIG_READABLE`、`CONFIG_PROTECTED_CONTENT`、`CONFIG_HAS_STENCIL_BUFFER` 等

## 关键实现逻辑

- **构造** -- 支持原生窗口指针或宽高尺寸两种方式创建
- **makeCurrent()** -- 将交换链设为当前渲染目标
- **commit()** -- 提交帧缓冲区到显示系统
- **initFlags()** -- 根据后端能力过滤不支持的配置标志
- **recreateWithNewFlags()** -- 调试用途，允许用新标志重建交换链
- **静态查询方法** -- `isSRGBSwapChainSupported()`、`isProtectedContentSupported()` 等

## 依赖关系

- `details/Engine.h` -- 引擎实例
- `private/backend/DriverApi.h` -- 驱动 API
- `backend/CallbackHandler.h` -- 回调处理

## 与公共 API 的关系

直接对应 `filament::SwapChain`。通过 `Engine::createSwapChain()` 创建，传递给 `Renderer::beginFrame()` 作为渲染目标。
