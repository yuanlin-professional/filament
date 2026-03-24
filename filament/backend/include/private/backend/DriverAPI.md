# DriverAPI.inc 文档

## 1. 概述

`DriverAPI.inc` 是 Filament 图形引擎后端的核心头文件，采用 **X-Macro** 模式定义了所有图形驱动 API。该文件**没有 include guard**（文件头尾均有注释强调），设计上允许被多次 `#include`，每次包含前使用方需先定义一组宏，文件展开后会自动 `#undef` 所有宏。

通过这种机制，同一份 API 声明可被复用于：
- 生成虚函数声明（Driver 接口类）
- 生成命令分发代码（CommandStream）
- 生成命令序列化/反序列化逻辑

---

## 2. 宏定义详解

### 2.1 三大核心宏

使用方在 `#include "DriverAPI.inc"` 之前**必须**定义以下三个宏，否则编译报错：

| 宏 | 签名 | 行为 | 说明 |
|---|---|---|---|
| `DECL_DRIVER_API(M, D, P)` | `M`=方法名, `D`=带类型参数列表, `P`=不带类型参数列表 | **异步命令** | 命令入队到循环缓冲区（CommandStream），由渲染线程执行，无返回值 |
| `DECL_DRIVER_API_SYNCHRONOUS(R, M, D, P)` | `R`=返回类型, `M`=方法名, `D`=带类型参数列表, `P`=不带类型参数列表 | **同步调用** | 直接在调用线程执行，阻塞等待并返回结果 |
| `DECL_DRIVER_API_RETURN(R, M, D, P)` | `R`=返回类型, `M`=方法名, `D`=带类型参数列表, `P`=不带类型参数列表 | **两阶段异步** | 立即返回句柄（`methodNameS` 阶段），副作用异步执行（`methodNameR` 阶段） |

#### 参数列表展开示例

以 `beginFrame` 为例，说明 `D (params_decl)` 和 `P (params)` 的区别。

**原始声明**（`DriverAPI.inc` 中）：

```cpp
DECL_DRIVER_API_N(beginFrame,
        int64_t, monotonic_clock_ns,
        int64_t, refreshIntervalNs,
        uint32_t, frameId)
```

**宏展开过程**：

`DECL_DRIVER_API_N` 的定义为：

```cpp
#define DECL_DRIVER_API_N(N, ...) \
    DECL_DRIVER_API(N, PAIR_ARGS_N(ARG, ##__VA_ARGS__), PAIR_ARGS_N(PARAM, ##__VA_ARGS__))
```

它将 `(类型, 名称)` 对分别通过 `ARG` 和 `PARAM` 宏展开为两份列表，传给 `DECL_DRIVER_API(M, D, P)`：

| 宏 | 展开规则 | 展开结果 | 用途 |
|---|---|---|---|
| `ARG(T, P)` → `T P` | `PAIR_ARGS_N(ARG, int64_t, monotonic_clock_ns, ...)` | `int64_t monotonic_clock_ns, int64_t refreshIntervalNs, uint32_t frameId` | **D (params_decl)** — 函数声明/定义 |
| `PARAM(T, P)` → `P` | `PAIR_ARGS_N(PARAM, int64_t, monotonic_clock_ns, ...)` | `monotonic_clock_ns, refreshIntervalNs, frameId` | **P (params)** — 函数调用时转发参数 |

**最终展开结果**：

```cpp
DECL_DRIVER_API(beginFrame,
    /* D = params_decl */ int64_t monotonic_clock_ns, int64_t refreshIntervalNs, uint32_t frameId,
    /* P = params      */ monotonic_clock_ns, refreshIntervalNs, frameId)
```

**实际使用对比**：

```cpp
// 生成函数声明时使用 D（带类型）：
virtual void beginFrame(int64_t monotonic_clock_ns, int64_t refreshIntervalNs, uint32_t frameId);

// 转发调用时使用 P（仅参数名）：
driver->beginFrame(monotonic_clock_ns, refreshIntervalNs, frameId);
```

### 2.2 带参数的便捷宏

这些宏接受 `(类型, 参数名)` 对的变长参数列表，内部通过 `PAIR_ARGS_N` 展开为正式的参数声明和参数传递列表：

| 宏 | 展开目标 | 说明 |
|---|---|---|
| `DECL_DRIVER_API_N(N, ...)` | `DECL_DRIVER_API` | 异步命令，带 N 个参数 |
| `DECL_DRIVER_API_R_N(R, N, ...)` | `DECL_DRIVER_API_RETURN` | 两阶段异步，带 N 个参数 |
| `DECL_DRIVER_API_TAGGED_R_N(R, N, ...)` | `DECL_DRIVER_API_RETURN` | 两阶段异步，自动追加 `utils::ImmutableCString&& tag` 调试标签参数 |
| `DECL_DRIVER_API_SYNCHRONOUS_N(R, N, ...)` | `DECL_DRIVER_API_SYNCHRONOUS` | 同步调用，带 N 个参数 |
| `DECL_DRIVER_API_SYNCHRONOUS_TAGGED_N(R, N, ...)` | `DECL_DRIVER_API_SYNCHRONOUS` | 同步调用，自动追加 `utils::ImmutableCString tag` 调试标签参数 |

### 2.3 零参数变体

由于 C++14 不支持 `__VA_OPT__()`，空参数列表无法通过 `##__VA_ARGS__` 正确处理，因此提供了特殊的零参数宏：

| 宏 | 展开目标 | 说明 |
|---|---|---|
| `DECL_DRIVER_API_0(N)` | `DECL_DRIVER_API` | 异步命令，零参数（内部传入 `int dummy=0` 占位） |
| `DECL_DRIVER_API_R_0(R, N)` | `DECL_DRIVER_API_RETURN` | 两阶段异步，零参数 |
| `DECL_DRIVER_API_SYNCHRONOUS_0(R, N)` | `DECL_DRIVER_API_SYNCHRONOUS` | 同步调用，零参数 |

### 2.4 底层参数展开工具宏

| 宏 | 作用 |
|---|---|
| `EXPAND(x)` | 辅助宏展开，解决 MSVC 等编译器的宏展开问题 |
| `APPLY(M, ...)` | 对变长参数列表中的每个元素依次应用宏 `M`，最多支持 13 个参数 |
| `PAIR_ARGS_N(M, ...)` | 将变长参数列表按 `(类型, 名称)` 成对分组，对每对应用宏 `M`，最多支持 13 对 |
| `ARG(T, P)` | 展开为 `T P`（带类型的参数声明，如 `uint32_t width`） |
| `PARAM(T, P)` | 展开为 `P`（仅参数名，如 `width`） |

---

## 3. 图形 API 列表

> **图例**：
> - **异步** = `DECL_DRIVER_API` 系列（命令入队，渲染线程执行）
> - **同步** = `DECL_DRIVER_API_SYNCHRONOUS` 系列（调用线程直接执行）
> - **两阶段** = `DECL_DRIVER_API_RETURN` 系列（立即返回句柄，副作用异步执行）

---

### 3.1 帧控制

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `tick` | （无） | 异步 | 驱动心跳，每帧调用，用于内部维护任务 |
| `beginFrame` | `int64_t monotonic_clock_ns`, `int64_t refreshIntervalNs`, `uint32_t frameId` | 异步 | 开始新帧，传入单调时钟时间戳、刷新间隔和帧 ID |
| `endFrame` | `uint32_t frameId` | 异步 | 结束当前帧 |
| `flush` | （无） | 异步 | 提示驱动可以开始渲染已提交的命令（如 GL 后端对应 `glFlush()`） |
| `finish` | （无） | 异步 | 刷新并等待所有命令执行完毕 |
| `resetState` | （无） | 异步 | 重置驱动的状态追踪（如 GL 后端的状态缓存） |

---

### 3.2 回调设置

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `setFrameScheduledCallback` | `SwapChainHandle sch`, `CallbackHandler* handler`, `FrameScheduledCallback&& callback`, `uint64_t flags` | 异步 | 设置帧调度回调，当帧被调度执行时触发 |
| `setFrameCompletedCallback` | `SwapChainHandle sch`, `CallbackHandler* handler`, `Invocable<void(void)>&& callback` | 异步 | 设置帧完成回调，当帧渲染完毕时触发 |
| `setPresentationTime` | `int64_t monotonic_clock_ns` | 异步 | 设置期望的呈现时间戳 |

---

### 3.3 创建驱动对象

所有创建 API 均为**两阶段异步**（`DECL_DRIVER_API_TAGGED_R_N`），立即返回句柄，实际资源创建异步执行。带 `tag` 参数用于调试标识。

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `createVertexBufferInfo` | `VertexBufferInfoHandle` | `uint8_t bufferCount`, `uint8_t attributeCount`, `AttributeArray attributes` | 创建顶点缓冲区信息描述（属性布局） |
| `createVertexBuffer` | `VertexBufferHandle` | `uint32_t vertexCount`, `VertexBufferInfoHandle vbih` | 创建顶点缓冲区 |
| `createIndexBuffer` | `IndexBufferHandle` | `ElementType elementType`, `uint32_t indexCount`, `BufferUsage usage` | 创建索引缓冲区 |
| `createIndexBufferAsync` | `IndexBufferHandle` | `ElementType elementType`, `uint32_t indexCount`, `BufferUsage usage`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 异步版创建索引缓冲区，创建完成后触发回调 |
| `createBufferObject` | `BufferObjectHandle` | `uint32_t byteCount`, `BufferObjectBinding bindingType`, `BufferUsage usage` | 创建通用缓冲区对象 |
| `createBufferObjectAsync` | `BufferObjectHandle` | `uint32_t byteCount`, `BufferObjectBinding bindingType`, `BufferUsage usage`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 异步版创建缓冲区对象，完成后触发回调 |
| `createTexture` | `TextureHandle` | `SamplerType target`, `uint8_t levels`, `TextureFormat format`, `uint8_t samples`, `uint32_t width`, `uint32_t height`, `uint32_t depth`, `TextureUsage usage` | 创建纹理 |
| `createTextureAsync` | `TextureHandle` | `SamplerType target`, `uint8_t levels`, `TextureFormat format`, `uint8_t samples`, `uint32_t width`, `uint32_t height`, `uint32_t depth`, `TextureUsage usage`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 异步版创建纹理，完成后触发回调 |
| `createTextureView` | `TextureHandle` | `TextureHandle texture`, `uint8_t baseLevel`, `uint8_t levelCount` | 创建纹理视图（指定 mip 级别范围） |
| `createTextureViewSwizzle` | `TextureHandle` | `TextureHandle texture`, `TextureSwizzle r`, `TextureSwizzle g`, `TextureSwizzle b`, `TextureSwizzle a` | 创建带通道重映射（swizzle）的纹理视图 |
| `createTextureViewSwizzleAsync` | `TextureHandle` | `TextureHandle texture`, `TextureSwizzle r`, `TextureSwizzle g`, `TextureSwizzle b`, `TextureSwizzle a`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 异步版创建带 swizzle 的纹理视图 |
| `createTextureExternalImage2` | `TextureHandle` | `SamplerType target`, `TextureFormat format`, `uint32_t width`, `uint32_t height`, `TextureUsage usage`, `Platform::ExternalImageHandleRef image` | 从平台外部图像句柄创建纹理（新版接口） |
| `createTextureExternalImage` | `TextureHandle` | `SamplerType target`, `TextureFormat format`, `uint32_t width`, `uint32_t height`, `TextureUsage usage`, `void* image` | 从外部图像指针创建纹理 |
| `createTextureExternalImagePlane` | `TextureHandle` | `TextureFormat format`, `uint32_t width`, `uint32_t height`, `TextureUsage usage`, `void* image`, `uint32_t plane` | 从外部图像的指定平面创建纹理（多平面格式如 YUV） |
| `importTexture` | `TextureHandle` | `intptr_t id`, `SamplerType target`, `uint8_t levels`, `TextureFormat format`, `uint8_t samples`, `uint32_t width`, `uint32_t height`, `uint32_t depth`, `TextureUsage usage` | 导入外部原生纹理对象（如 GL 纹理 ID） |
| `importTextureAsync` | `TextureHandle` | `intptr_t id`, `SamplerType target`, `uint8_t levels`, `TextureFormat format`, `uint8_t samples`, `uint32_t width`, `uint32_t height`, `uint32_t depth`, `TextureUsage usage`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 异步版导入外部纹理 |
| `createRenderPrimitive` | `RenderPrimitiveHandle` | `VertexBufferHandle vbh`, `IndexBufferHandle ibh`, `PrimitiveType pt` | 创建渲染图元（绑定顶点缓冲、索引缓冲和图元类型） |
| `createProgram` | `ProgramHandle` | `Program&& program` | 创建着色器程序 |
| `createDefaultRenderTarget` | `RenderTargetHandle` | （无） | 创建默认渲染目标（通常为屏幕 FBO） |
| `createRenderTarget` | `RenderTargetHandle` | `TargetBufferFlags targetBufferFlags`, `uint32_t width`, `uint32_t height`, `uint8_t samples`, `uint8_t layerCount`, `MRT color`, `TargetBufferInfo depth`, `TargetBufferInfo stencil` | 创建自定义渲染目标（FBO），支持 MRT、多采样和多层 |
| `createFence` | `FenceHandle` | （无） | 创建 CPU 端栅栏同步对象 |
| `createSync` | `SyncHandle` | （无） | 创建 GPU/平台端同步对象 |
| `createSwapChain` | `SwapChainHandle` | `void* nativeWindow`, `uint64_t flags` | 为原生窗口创建交换链 |
| `createSwapChainHeadless` | `SwapChainHandle` | `uint32_t width`, `uint32_t height`, `uint64_t flags` | 创建无窗口（headless）交换链 |
| `createTimerQuery` | `TimerQueryHandle` | （无） | 创建 GPU 计时查询对象 |
| `createDescriptorSetLayout` | `DescriptorSetLayoutHandle` | `DescriptorSetLayout&& info` | 创建描述符集布局 |
| `createDescriptorSet` | `DescriptorSetHandle` | `DescriptorSetLayoutHandle dslh` | 根据布局创建描述符集 |
| `mapBuffer` | `MemoryMappedBufferHandle` | `BufferObjectHandle boh`, `size_t offset`, `size_t size`, `MapBufferAccessFlags access` | 映射缓冲区到 CPU 可访问内存 |

---

### 3.4 销毁驱动对象

所有销毁 API 均为**异步**（`DECL_DRIVER_API_N`），命令入队后由渲染线程执行实际销毁。

| API | 参数 | 说明 |
|---|---|---|
| `destroyVertexBuffer` | `VertexBufferHandle vbh` | 销毁顶点缓冲区 |
| `destroyVertexBufferInfo` | `VertexBufferInfoHandle vbih` | 销毁顶点缓冲区信息 |
| `destroyIndexBuffer` | `IndexBufferHandle ibh` | 销毁索引缓冲区 |
| `destroyBufferObject` | `BufferObjectHandle boh` | 销毁缓冲区对象 |
| `destroyRenderPrimitive` | `RenderPrimitiveHandle rph` | 销毁渲染图元 |
| `destroyProgram` | `ProgramHandle ph` | 销毁着色器程序 |
| `destroyTexture` | `TextureHandle th` | 销毁纹理 |
| `destroyRenderTarget` | `RenderTargetHandle rth` | 销毁渲染目标 |
| `destroySwapChain` | `SwapChainHandle sch` | 销毁交换链 |
| `destroyStream` | `StreamHandle sh` | 销毁流对象 |
| `destroyTimerQuery` | `TimerQueryHandle sh` | 销毁计时查询 |
| `destroyFence` | `FenceHandle fh` | 销毁栅栏 |
| `destroySync` | `SyncHandle sh` | 销毁同步对象 |
| `destroyDescriptorSetLayout` | `DescriptorSetLayoutHandle dslh` | 销毁描述符集布局 |
| `destroyDescriptorSet` | `DescriptorSetHandle dsh` | 销毁描述符集 |
| `unmapBuffer` | `MemoryMappedBufferHandle mmbh` | 取消缓冲区的内存映射 |

---

### 3.5 同步 API

所有同步 API 在调用线程直接执行，阻塞等待并返回结果。

#### 3.5.1 驱动生命周期

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `terminate` | `void` | （无） | 终止驱动，释放所有资源 |

#### 3.5.2 流操作

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `createStreamNative` | `StreamHandle` | `void* stream` | 从原生流对象创建流（带 tag） |
| `createStreamAcquired` | `StreamHandle` | （无） | 创建采集模式的流（带 tag） |
| `setAcquiredImage` | `void` | `StreamHandle stream`, `void* image`, `const mat3f& transform`, `CallbackHandler* handler`, `StreamCallback cb`, `void* userData` | 设置采集流的当前图像及变换矩阵 |
| `setStreamDimensions` | `void` | `StreamHandle stream`, `uint32_t width`, `uint32_t height` | 设置流的尺寸 |
| `getStreamTimestamp` | `int64_t` | `StreamHandle stream` | 获取流的最新时间戳 |
| `updateStreams` | `void` | `DriverApi* driver` | 更新所有流的状态 |

#### 3.5.3 栅栏与同步

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `getFenceStatus` | `FenceStatus` | `FenceHandle fh` | 非阻塞查询栅栏状态 |
| `fenceWait` | `FenceStatus` | `FenceHandle fh`, `uint64_t timeout` | 阻塞等待栅栏信号，支持超时 |
| `fenceCancel` | `void` | `FenceHandle fh` | 取消等待中的栅栏 |
| `getPlatformSync` | `void` | `SyncHandle sh`, `CallbackHandler* handler`, `Platform::SyncCallback cb`, `void* userData` | 获取平台端同步对象，通过回调返回 |

#### 3.5.4 能力查询

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `isTextureFormatSupported` | `bool` | `TextureFormat format` | 查询是否支持指定纹理格式 |
| `isTextureSwizzleSupported` | `bool` | （无） | 查询是否支持纹理通道重映射 |
| `isTextureFormatMipmappable` | `bool` | `TextureFormat format` | 查询指定纹理格式是否支持自动生成 mipmap |
| `isRenderTargetFormatSupported` | `bool` | `TextureFormat format` | 查询是否支持指定格式作为渲染目标 |
| `isFrameBufferFetchSupported` | `bool` | （无） | 查询是否支持帧缓冲读取（subpass input） |
| `isFrameBufferFetchMultiSampleSupported` | `bool` | （无） | 查询是否支持多采样帧缓冲读取 |
| `isFrameTimeSupported` | `bool` | （无） | 查询是否支持帧时间统计 |
| `isAutoDepthResolveSupported` | `bool` | （无） | 查询是否支持自动深度解析 |
| `isSRGBSwapChainSupported` | `bool` | （无） | 查询是否支持 sRGB 交换链 |
| `isMSAASwapChainSupported` | `bool` | `uint32_t samples` | 查询是否支持指定采样数的 MSAA 交换链 |
| `isProtectedContentSupported` | `bool` | （无） | 查询是否支持受保护内容（DRM） |
| `isStereoSupported` | `bool` | （无） | 查询是否支持立体渲染 |
| `isParallelShaderCompileSupported` | `bool` | （无） | 查询是否支持并行着色器编译 |
| `isDepthStencilResolveSupported` | `bool` | （无） | 查询是否支持深度/模板解析 |
| `isDepthStencilBlitSupported` | `bool` | `TextureFormat format` | 查询是否支持指定格式的深度/模板 blit |
| `isProtectedTexturesSupported` | `bool` | （无） | 查询是否支持受保护纹理 |
| `isDepthClampSupported` | `bool` | （无） | 查询是否支持深度钳位 |
| `isAsynchronousModeEnabled` | `bool` | （无） | 查询是否启用了异步模式 |
| `isCompositorTimingSupported` | `bool` | （无） | 查询是否支持合成器时序 |
| `isWorkaroundNeeded` | `bool` | `Workaround workaround` | 查询是否需要指定的驱动兼容性补丁 |

#### 3.5.5 限制值查询

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `getMaxDrawBuffers` | `uint8_t` | （无） | 获取最大绘制缓冲区数（MRT） |
| `getMaxUniformBufferSize` | `size_t` | （无） | 获取 Uniform Buffer 最大字节数 |
| `getMaxTextureSize` | `size_t` | `SamplerType target` | 获取指定类型纹理的最大尺寸 |
| `getMaxArrayTextureLayers` | `size_t` | （无） | 获取数组纹理最大层数 |
| `getClipSpaceParams` | `math::float2` | （无） | 获取裁剪空间参数（近平面 z 值和方向） |
| `getFeatureLevel` | `FeatureLevel` | （无） | 获取当前驱动的功能级别 |
| `getUniformBufferOffsetAlignment` | `size_t` | （无） | 获取 Uniform Buffer 偏移对齐要求 |

#### 3.5.6 外部图像与计时

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `setupExternalImage2` | `void` | `Platform::ExternalImageHandleRef image` | 设置外部图像（新版接口，使用平台句柄引用） |
| `setupExternalImage` | `void` | `void* image` | 设置外部图像（旧版接口，使用原始指针） |
| `getTimerQueryValue` | `TimerQueryResult` | `TimerQueryHandle tqh`, `uint64_t* elapsedTime` | 获取 GPU 计时查询结果 |

#### 3.5.7 帧时序查询

| API | 返回类型 | 参数 | 说明 |
|---|---|---|---|
| `queryFrameTimestamps` | `bool` | `SwapChainHandle swapChain`, `uint64_t frameId`, `FrameTimestamps* outFrameTimestamps` | 查询指定帧的时间戳信息 |
| `queryCompositorTiming` | `bool` | `SwapChainHandle swapChain`, `CompositorTiming* outCompositorTiming` | 查询合成器时序信息 |

---

### 3.6 更新驱动对象

#### 3.6.1 缓冲区更新

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `setVertexBufferObject` | `VertexBufferHandle vbh`, `uint32_t index`, `BufferObjectHandle bufferObject` | 异步 | 将缓冲区对象绑定到顶点缓冲区的指定槽位 |
| `setVertexBufferObjectAsync` | `VertexBufferHandle vbh`, `uint32_t index`, `BufferObjectHandle bufferObject`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 两阶段（返回 `AsyncCallId`） | 异步版绑定缓冲区对象到顶点缓冲区槽位 |
| `updateIndexBuffer` | `IndexBufferHandle ibh`, `BufferDescriptor&& data`, `uint32_t byteOffset` | 异步 | 更新索引缓冲区数据 |
| `updateIndexBufferAsync` | `IndexBufferHandle ibh`, `BufferDescriptor&& data`, `uint32_t byteOffset`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 两阶段（返回 `AsyncCallId`） | 异步版更新索引缓冲区，完成后回调 |
| `updateBufferObject` | `BufferObjectHandle ibh`, `BufferDescriptor&& data`, `uint32_t byteOffset` | 异步 | 更新缓冲区对象数据 |
| `updateBufferObjectAsync` | `BufferObjectHandle ibh`, `BufferDescriptor&& data`, `uint32_t byteOffset`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 两阶段（返回 `AsyncCallId`） | 异步版更新缓冲区对象，完成后回调 |
| `updateBufferObjectUnsynchronized` | `BufferObjectHandle boh`, `BufferDescriptor&& data`, `uint32_t byteOffset` | 异步 | 非同步更新缓冲区对象（无内存屏障，调用者需自行保证安全） |
| `resetBufferObject` | `BufferObjectHandle boh` | 异步 | 重置缓冲区对象 |

#### 3.6.2 纹理更新

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `update3DImage` | `TextureHandle th`, `uint32_t level`, `uint32_t xoffset`, `uint32_t yoffset`, `uint32_t zoffset`, `uint32_t width`, `uint32_t height`, `uint32_t depth`, `PixelBufferDescriptor&& data` | 异步 | 更新纹理的 3D 子区域数据（也可用于 2D 纹理） |
| `update3DImageAsync` | `TextureHandle th`, `uint32_t level`, `uint32_t xoffset`, `uint32_t yoffset`, `uint32_t zoffset`, `uint32_t width`, `uint32_t height`, `uint32_t depth`, `PixelBufferDescriptor&& data`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 两阶段（返回 `AsyncCallId`） | 异步版更新纹理数据，完成后回调 |
| `generateMipmaps` | `TextureHandle th` | 异步 | 自动生成纹理的 mipmap 链 |
| `setExternalStream` | `TextureHandle th`, `StreamHandle sh` | 异步 | 将纹理绑定到外部流 |

#### 3.6.3 计时器查询

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `beginTimerQuery` | `TimerQueryHandle tqh` | 异步 | 开始 GPU 计时查询 |
| `endTimerQuery` | `TimerQueryHandle tqh` | 异步 | 结束 GPU 计时查询 |

#### 3.6.4 着色器编译

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `compilePrograms` | `CompilerPriorityQueue priority`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 异步 | 触发着色器程序编译，支持优先级队列和完成回调 |

#### 3.6.5 描述符集更新

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `updateDescriptorSetBuffer` | `DescriptorSetHandle dsh`, `descriptor_binding_t binding`, `BufferObjectHandle boh`, `uint32_t offset`, `uint32_t size` | 异步 | 更新描述符集中的缓冲区绑定 |
| `updateDescriptorSetTexture` | `DescriptorSetHandle dsh`, `descriptor_binding_t binding`, `TextureHandle th`, `SamplerParams params` | 异步 | 更新描述符集中的纹理/采样器绑定 |

#### 3.6.6 内存映射与推送常量

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `copyToMemoryMappedBuffer` | `MemoryMappedBufferHandle mmbh`, `size_t offset`, `BufferDescriptor&& data` | 异步 | 向内存映射缓冲区写入数据 |
| `setPushConstant` | `ShaderStage stage`, `uint8_t index`, `PushConstantVariant value` | 异步 | 设置推送常量值 |

---

### 3.7 渲染状态设置

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `beginRenderPass` | `RenderTargetHandle rth`, `const RenderPassParams& params` | 异步 | 开始渲染通道，绑定渲染目标并设置清除/加载/存储操作 |
| `endRenderPass` | （无） | 异步 | 结束当前渲染通道 |
| `nextSubpass` | （无） | 异步 | 进入下一个子通道 |
| `bindPipeline` | `const PipelineState& state` | 异步 | 绑定渲染管线状态（着色器、混合、深度测试等） |
| `bindRenderPrimitive` | `RenderPrimitiveHandle rph` | 异步 | 绑定渲染图元（顶点/索引缓冲和图元类型） |
| `bindDescriptorSet` | `DescriptorSetHandle dsh`, `descriptor_set_t set`, `DescriptorSetOffsetArray&& offsets` | 异步 | 绑定描述符集到指定集合槽位，支持动态偏移 |
| `scissor` | `Viewport scissor` | 异步 | 设置裁剪矩形 |

---

### 3.8 交换链操作

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `makeCurrent` | `SwapChainHandle schDraw`, `SwapChainHandle schRead` | 异步 | 设置当前绘制和读取交换链 |
| `commit` | `SwapChainHandle sch` | 异步 | 提交交换链（执行 present/swap） |

---

### 3.9 调试标记

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `insertEventMarker` | `const char* string` | 异步 | 插入事件标记（在 GPU 调试工具中可见） |
| `pushGroupMarker` | `const char* string` | 异步 | 压入分组标记（开始命令组） |
| `popGroupMarker` | （无） | 异步 | 弹出分组标记（结束命令组） |
| `startCapture` | （无） | 异步 | 开始 GPU 帧捕获（配合 RenderDoc 等工具） |
| `stopCapture` | （无） | 异步 | 停止 GPU 帧捕获 |

---

### 3.10 回读操作

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `readPixels` | `RenderTargetHandle src`, `uint32_t x`, `uint32_t y`, `uint32_t width`, `uint32_t height`, `PixelBufferDescriptor&& data` | 异步 | 从渲染目标读取像素数据到 CPU 缓冲区 |
| `readTexture` | `TextureHandle src`, `uint8_t level`, `uint16_t layer`, `uint32_t x`, `uint32_t y`, `uint32_t width`, `uint32_t height`, `PixelBufferDescriptor&& data` | 异步 | 从纹理指定层级和区域读取像素数据 |
| `readBufferSubData` | `BufferObjectHandle src`, `uint32_t offset`, `uint32_t size`, `BufferDescriptor&& data` | 异步 | 从缓冲区对象读取指定范围的数据 |

---

### 3.11 绘制与计算

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `draw2` | `uint32_t indexOffset`, `uint32_t indexCount`, `uint32_t instanceCount` | 异步 | 绘制调用（使用已绑定的管线和图元） |
| `draw` | `PipelineState state`, `RenderPrimitiveHandle rph`, `uint32_t indexOffset`, `uint32_t indexCount`, `uint32_t instanceCount` | 异步 | 绘制调用（自包含，传入管线状态和图元） |
| `blit` | `TextureHandle dst`, `uint8_t dstLevel`, `uint8_t dstLayer`, `math::uint2 dstOrigin`, `TextureHandle src`, `uint8_t srcLevel`, `uint8_t srcLayer`, `math::uint2 srcOrigin`, `math::uint2 size` | 异步 | 纹理间拷贝（blit），支持指定源/目标的级别、层、偏移和尺寸 |
| `resolve` | `TextureHandle dst`, `uint8_t dstLevel`, `uint8_t dstLayer`, `TextureHandle src`, `uint8_t srcLevel`, `uint8_t srcLayer` | 异步 | 多采样纹理解析（MSAA resolve） |
| `blitDEPRECATED` | `TargetBufferFlags buffers`, `RenderTargetHandle dst`, `Viewport dstRect`, `RenderTargetHandle src`, `Viewport srcRect`, `SamplerMagFilter filter` | 异步 | **已弃用** — 渲染目标间 blit，使用视口指定区域 |
| `dispatchCompute` | `ProgramHandle program`, `math::uint3 workGroupCount` | 异步 | 分派计算着色器，指定工作组数量 |

---

### 3.12 异步操作辅助

| API | 参数 | 类型 | 说明 |
|---|---|---|---|
| `queueCommandAsync` | `Invocable<void(void)>&& command`, `CallbackHandler* handler`, `CallbackHandler::Callback callback`, `void* user` | 两阶段（返回 `AsyncCallId`） | 将自定义命令入队异步执行，完成后触发回调 |
| `cancelAsyncJob` | `AsyncCallId jobId` | 同步（返回 `bool`） | 取消指定的异步任务，返回是否成功取消 |

---

## 4. 使用示例

以下示例展示如何利用 X-Macro 模式将 `DriverAPI.inc` 中的声明自动展开为 C++ 类的虚方法：

```cpp
class MyDriver : public Driver {
public:

    // 定义异步 API 的展开方式：生成虚方法声明
    #define DECL_DRIVER_API(method_name, params_decl, params) \
        virtual void method_name(params_decl) override;

    // 定义同步 API 的展开方式：生成带返回值的虚方法
    #define DECL_DRIVER_API_SYNCHRONOUS(RetType, method_name, params_decl, params) \
        virtual RetType method_name(params_decl) override;

    // 定义两阶段 API 的展开方式：生成返回句柄的虚方法
    #define DECL_DRIVER_API_RETURN(RetType, method_name, params_decl, params) \
        virtual RetType method_name(params_decl) override;

    // 包含 .inc 文件，自动展开所有 API
    #include "DriverAPI.inc"

    // 宏已在 .inc 文件末尾自动 #undef，无需手动清理
};
```

展开后，`MyDriver` 类中将包含所有驱动 API 的虚方法声明，如：

```cpp
virtual void tick(int dummy=0) override;
virtual void beginFrame(int64_t monotonic_clock_ns, int64_t refreshIntervalNs, uint32_t frameId) override;
virtual backend::VertexBufferInfoHandle createVertexBufferInfo(uint8_t bufferCount, uint8_t attributeCount, backend::AttributeArray attributes, utils::ImmutableCString&& tag = {}) override;
virtual void terminate() override;
// ... 其余约 140 个 API
```

这种模式的优势在于：**只需维护一份 API 定义**（`DriverAPI.inc`），即可通过不同的宏定义在多处生成一致的代码。
