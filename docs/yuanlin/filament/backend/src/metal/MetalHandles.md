# MetalHandles

## 文件概述

MetalHandles 定义了 Metal 后端所有 GPU 资源的句柄类型（Handle Types），是 Filament 硬件抽象层（Hw* 基类）的 Metal 具体实现。涵盖交换链、缓冲区、纹理、渲染目标、着色器程序、描述符集、Fence、Timer Query 等全部资源类型。

**源文件**: `MetalHandles.h`, `MetalHandles.mm`

## 核心类/结构体

- **`MetalAttachment`** - 渲染通道附件封装，包含主纹理和可选的 MSAA sidecar 纹理
- **`MetalSwapChain`** - 交换链，支持三种模式：
  - `CAMETALLAYER` - 基于 CAMetalLayer 的屏幕渲染
  - `CVPIXELBUFFERREF` - 基于 CVPixelBuffer 的离屏渲染
  - `HEADLESS` - 无显示目标的离屏渲染
- **`MetalBufferObject`** / **`MetalVertexBuffer`** / **`MetalIndexBuffer`** - 缓冲区对象
- **`MetalVertexBufferInfo`** - 顶点缓冲区布局信息，负责源缓冲区到 Metal buffer argument 的映射
- **`MetalRenderPrimitive`** - 渲染图元，弱引用顶点和索引缓冲区
- **`MetalProgram`** - 着色器程序，持有异步编译的 `MetalFunctionBundle` token
- **`MetalTexture`** - 纹理对象，支持常规纹理、纹理视图（LOD/swizzle）、导入纹理、外部图像纹理
- **`MetalRenderTarget`** - 渲染目标，管理颜色/深度/模板附件和 MSAA 自动 resolve
- **`MetalFence`** / **`MetalSync`** - GPU 同步原语，基于 `MTLSharedEvent`
- **`MetalTimerQuery`** - GPU 时间查询
- **`MetalDescriptorSetLayout`** / **`MetalDescriptorSet`** - 描述符集布局和实例
- **`MetalMemoryMappedBuffer`** - 内存映射缓冲区

## 关键实现逻辑

- **SwapChain drawable 管理**: CAMetalLayer 的 nextDrawable 通过 mutex 保护；获取失败时根据策略 panic 或放弃当前帧
- **MSAA 自动 resolve**: 当渲染目标 samples > 1 但纹理是单采样时，自动创建 MSAA sidecar 纹理并在渲染通道结束时 resolve
- **顶点布局映射**: `MetalVertexBufferInfo` 将多个共享相同 stride 的源缓冲区合并到同一 Metal buffer argument index
- **纹理上传双策略**: `loadWithCopyBuffer`（小纹理，通过 blit 编码器拷贝）和 `loadWithBlit`（大纹理或需要格式转换，通过渲染 blit）
- **描述符集**: 使用 Metal Argument Buffer 机制，为每个 shader stage 分别编码缓冲区/纹理/采样器绑定
- **帧回调**: `PresentDrawableData` 管理 drawable 的生命周期和帧调度/完成回调
- **Fence**: 基于 `MTLSharedEvent` 实现 GPU-CPU 同步，iOS 12+ 可用

## 依赖关系

- `MetalBuffer.h`、`MetalContext.h`、`MetalEnums.h`、`MetalExternalImage.h`、`MetalFlags.h`、`MetalState.h` - Metal 后端内部模块
- `MetalBlitter.h`、`MetalBufferPool.h`、`MetalUtils.h` - 纹理上传和 blit
- `backend/DriverEnums.h`、`backend/platforms/PlatformMetal.h` - 引擎接口
- `Metal/Metal.h`、`QuartzCore/QuartzCore.h`、`CoreVideo/CVPixelBuffer.h`
