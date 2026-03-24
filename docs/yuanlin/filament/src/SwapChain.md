# SwapChain.cpp

## 文件概述
SwapChain.cpp 实现了交换链的公共 API。SwapChain 代表一个可显示的渲染表面，通常关联到一个原生窗口，是渲染结果最终呈现的目标。

## 核心类/结构体/函数
- **SwapChain** - 交换链公共接口
  - `getNativeWindow()` - 获取原生窗口指针
  - `setFrameScheduledCallback()` - 设置帧调度回调（帧准备好呈现时触发）
  - `isFrameScheduledCallbackSet()` - 检查是否设置了帧调度回调
  - `setFrameCompletedCallback()` - 设置帧完成回调
  - `isSRGBSwapChainSupported()` - 检查是否支持 sRGB 交换链
  - `isMSAASwapChainSupported()` - 检查是否支持 MSAA 交换链
  - `isProtectedContentSupported()` - 检查是否支持受保护内容

## 关键实现逻辑
通过 `downcast` 委托给内部 `FSwapChain`。静态查询方法（如 `isSRGBSwapChainSupported`）直接调用 FSwapChain 的静态方法。

## 依赖关系
- `details/SwapChain.h` / `details/Engine.h` - 内部实现
- `backend/CallbackHandler.h` - 回调处理器

## 被引用关系
- `Renderer` - `beginFrame()` 接受 SwapChain 参数作为渲染目标
