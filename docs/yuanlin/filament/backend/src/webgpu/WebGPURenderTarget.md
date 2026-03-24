# WebGPURenderTarget

## 文件概述

`WebGPURenderTarget` 是 `HwRenderTarget` 的 WebGPU 实现，表示可渲染的目标集合（颜色附件、深度/模板附件）。支持默认渲染目标和自定义渲染目标，以及 MSAA sidecar 纹理。

## 核心类/结构体

- **`WebGPURenderTarget`** (继承 `HwRenderTarget`)
  - `mColorAttachments` - MRT 颜色附件信息
  - `mDepthAttachment` / `mStencilAttachment` - 深度/模板附件
  - `mColorAttachmentDesc` - 缓存的渲染通道颜色附件描述符
  - `mDepthStencilAttachmentDesc` - 缓存的深度/模板附件描述符

## 关键实现逻辑

1. **MSAA Sidecar 创建**: `createMsaaSidecarTextures` 在目标采样数 > 1 而附件纹理采样数 = 1 时自动创建 MSAA 侧车纹理
2. **渲染通道配置**: `setUpRenderPassAttachments` 根据默认/自定义目标和 MSAA 状态配置颜色和深度/模板附件
3. **Load/Store 操作**: `getLoadOperation` / `getStoreOperation` 根据 `RenderPassParams` 的 clear/discard 标志选择操作类型
4. **只读深度**: 支持深度只读模式，此时 depthLoadOp/depthStoreOp 设为 Undefined

## 依赖关系

- `WebGPUTexture.h` - 纹理类（MSAA sidecar 创建）
- `backend/TargetBufferInfo.h` - 附件信息
- `private/backend/BackendUtils.h` - `getTargetBufferFlagsAt` 等工具
