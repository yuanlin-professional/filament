# WebGPUSwapChain

## 文件概述

`WebGPUSwapChain` 管理 WebGPU 的交换链，负责帧缓冲区获取、呈现和尺寸调整。支持基于窗口表面的交换链和无头（headless）模式。

## 核心类/结构体

- **`WebGPUSwapChain`** (继承 `Platform::SwapChain` + `HwSwapChain`)
  - `mSurface` / `mConfig` - 窗口表面和配置
  - `mDepthTexture` / `mDepthTextureView` - 深度纹理及其视图
  - `mRenderTargetTextures` / `mRenderTargetViews` - 无头模式三重缓冲

## 关键实现逻辑

1. **颜色格式选择**: 优先 RGBA8/BGRA8 的 sRGB 或非 sRGB 变体
2. **深度格式选择**: 根据是否需要模板以及设备特性选择 `Depth32Float`、`Depth32FloatStencil8` 或 `Depth24PlusStencil8`
3. **呈现模式**: 固定使用 FIFO（垂直同步）
4. **Alpha 模式**: 优先 Auto/Inherit/Opaque，回退到 Premultiplied/Unpremultiplied
5. **动态调整大小**: `setExtent` 在尺寸变化时重新配置表面和深度纹理
6. **无头模式**: 使用 3 个离屏纹理循环渲染

## 依赖关系

- `WebGPUConstants.h` / `WebGPUStrings.h` - 调试输出
- `backend/Platform.h` - 帧调度回调
