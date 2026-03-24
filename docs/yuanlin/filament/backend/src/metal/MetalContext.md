# MetalContext

## 文件概述

MetalContext 是 Metal 后端的核心上下文结构体，汇聚了所有运行时状态，包括 Metal 设备、命令队列、当前渲染状态、各类状态缓存/跟踪器、资源管理器等。同时提供了推送常量缓冲区和动态偏移量管理等辅助类。

**源文件**: `MetalContext.h`, `MetalContext.mm`

## 核心类/结构体

- **`MetalContext`** - 全局上下文结构体，核心成员包括：
  - `device`、`commandQueue` - Metal 设备和命令队列
  - `pendingCommandBuffer` - 当前待提交的命令缓冲区
  - `currentRenderPassEncoder` - 当前渲染通道编码器
  - GPU 能力标志（纹理 swizzle、深度自动 resolve、memoryless RT、深度裁剪等）
  - 状态跟踪器：管线、深度/模板、面剔除、缠绕方向、深度裁剪、裁剪矩形
  - 状态缓存：管线状态、深度/模板状态、采样器状态、参数编码器
  - 描述符集绑定、动态偏移量、推送常量
  - 资源跟踪器 `MetalResourceTracker`
- **`MetalPushConstantBuffer`** - 管理推送常量值，支持 int/float/bool 类型，脏标记优化提交
- **`MetalDynamicOffsets`** - 管理描述符集的动态偏移量

## 关键实现逻辑

- **GPU 系列检测**: `initializeSupportedGpuFamilies()` 检测设备支持的 GPU 系列（Apple/Common/Mac），iOS 13+ 使用 `supportsFamily`，旧版使用 `supportsFeatureSet`
- **命令缓冲区管理**: `getPendingCommandBuffer()` 惰性创建命令缓冲区并注册完成回调；`submitPendingCommands()` 提交并递增 ID
- **命令缓冲区错误处理**: 完成回调中检查错误码，memoryless 限制达到时设置标志回退到 private 存储模式
- **推送常量**: `setBytes` 将变体类型序列化为 4 字节固定大小数组，bool 转为 uint32，按着色器阶段分别绑定
- **空纹理**: `getOrCreateEmptyTexture()` 惰性创建 1x1 BGRA 纹理，防止采样器未绑定纹理时的 GPU 错误

## 依赖关系

- `MetalErrorQueue.h` - 命令缓冲区错误队列
- `MetalResourceTracker.h` - 资源生命周期跟踪
- `MetalShaderCompiler.h` - 着色器编译器
- `MetalState.h` - 状态缓存和跟踪器类型定义
- `Metal/Metal.h`、`QuartzCore/QuartzCore.h`、`CoreVideo/CVMetalTextureCache.h`
