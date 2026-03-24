# DriverApi.h

## 文件概述

提供 `allocateFromCommandStream` 内联函数的实现，将命令流内存分配接口暴露给需要从命令流中分配内存的组件（如 `DescriptorSetOffsetArray`）。

## 核心函数

```cpp
inline void* allocateFromCommandStream(DriverApi& driver, size_t size, size_t alignment) noexcept {
    return driver.allocate(size, alignment);
}
```

该函数是对 `CommandStream::allocate()` 的简单包装，用于在命令流中分配临时内存。分配的内存在命令缓冲区处理完成后自动释放。

## 用途

主要被 `DescriptorSetOffsetArray` 使用，在命令流中分配偏移量数组的存储空间，确保数据与命令的生命周期一致。

## 依赖关系

- `backend/DriverApiForward.h` - `DriverApi` 类型
- `private/backend/CommandStream.h` - `CommandStream` 完整定义

---

# Filament 统一图形 API 规范

Filament 为所有图形后端（OpenGL、Vulkan、Metal、WebGPU、Noop）设计了一套统一接口规范。后端只需实现这套规范，上层代码完全不感知底层用的是哪个图形 API。

## 1. 规范的定义中心：DriverAPI.inc

整套规范定义在一个文件中：`filament/backend/include/private/backend/DriverAPI.inc`。

它 **没有 include guard**，被设计为反复包含。每次包含前定义三个宏，`DriverAPI.inc` 就用这三个宏"遍历"所有 API，由宏决定为每个 API 生成什么代码。

```cpp
// 三种 API 类型的宏签名：
DECL_DRIVER_API(methodName, paramsDecl, params)                         // 异步 API
DECL_DRIVER_API_SYNCHRONOUS(RetType, methodName, paramsDecl, params)    // 同步 API
DECL_DRIVER_API_RETURN(RetType, methodName, paramsDecl, params)         // 异步 + 返回 Handle
```

## 2. 同一份 DriverAPI.inc，六种用途

| 包含者 | 宏定义 | 生成结果 | 作用 |
|--------|--------|---------|------|
| **Driver.h** (基类) | 异步→空函数体 `{}`, 同步→`virtual ... = 0`, 返回→`virtual ...S() = 0` + 空 `...R(){}` | 基类接口声明 | 定义后端必须实现什么 |
| **各后端 .h** (VulkanDriver 等) | 异步→`inline void method(...)`, 同步→`override`, 返回→`...S() override` + `inline ...R()` | 后端方法声明 | 后端承诺实现的方法列表 |
| **CommandStream.h** | 异步→编码入队, 同步→直接调用, 返回→先拿 Handle 再入队 | 命令编码方法 | 上层调用入口 |
| **Dispatcher.h** | 异步→`Execute methodName_`, 同步→跳过 | 函数指针成员 | 分派表的字段 |
| **CommandStreamDispatcher.h** | 异步→生成分派静态方法, 同步→跳过 | 具体后端分派函数 | 连接 Command 和后端方法 |
| **CommandStream.cpp** (DEBUG) | 异步→实例化 log() 模板 | debug 日志方法 | 调试输出 |

### 具体展开对比

以 `draw2` (异步) 和 `isTextureFormatSupported` (同步) 和 `createTexture` (返回 Handle) 为例：

#### 在 Driver.h（基类）中展开

```cpp
// DECL_DRIVER_API → 空函数体（非虚！纯粹占位，给 CommandType 模板推导用）
void draw2(uint32_t indexOffset, uint32_t indexCount, uint32_t instanceCount) {}

// DECL_DRIVER_API_SYNCHRONOUS → 纯虚函数
virtual bool isTextureFormatSupported(TextureFormat format) = 0;

// DECL_DRIVER_API_RETURN → 两个函数
virtual TextureHandle createTextureS() noexcept = 0;           // 纯虚：同步分配 Handle
void createTextureR(TextureHandle, SamplerType, ...) {}        // 空函数体：占位
```

**为什么异步 API 在基类中是空函数而不是纯虚函数？**

因为异步 API 从不通过 `Driver` 基类的虚函数调用。它们通过 `ConcreteDispatcher` 的静态方法 + `static_cast` 直接调用具体后端，绕过了虚函数表。基类的空函数体仅用于 `CommandType` 模板的类型推导（`decltype(&Driver::draw2)` 获取参数类型）。

#### 在 VulkanDriver.h（后端）中展开

```cpp
// DECL_DRIVER_API → 非虚、inline
UTILS_ALWAYS_INLINE inline void draw2(uint32_t indexOffset, uint32_t indexCount, uint32_t instanceCount);

// DECL_DRIVER_API_SYNCHRONOUS → override
bool isTextureFormatSupported(TextureFormat format) override;

// DECL_DRIVER_API_RETURN → 两个方法
TextureHandle createTextureS() noexcept override;
UTILS_ALWAYS_INLINE inline void createTextureR(TextureHandle, SamplerType, ...);
```

#### 在 CommandStream.h（命令编码器）中展开

```cpp
// DECL_DRIVER_API → 序列化入队
inline void draw2(uint32_t indexOffset, uint32_t indexCount, uint32_t instanceCount) noexcept {
    using Cmd = COMMAND_TYPE(draw2);
    void* const p = allocateCommand(CommandBase::align(sizeof(Cmd)));
    new(p) Cmd(mDispatcher.draw2_, std::move(indexOffset), std::move(indexCount), std::move(instanceCount));
}

// DECL_DRIVER_API_SYNCHRONOUS → 直接调 Driver，不入队
inline bool isTextureFormatSupported(TextureFormat format) noexcept {
    return apply(&Driver::isTextureFormatSupported, mDriver, std::forward_as_tuple(format));
}

// DECL_DRIVER_API_RETURN → 先拿 Handle，再入队
inline TextureHandle createTexture(SamplerType target, ...) noexcept {
    TextureHandle result = mDriver.createTextureS();   // 同步拿 Handle
    using Cmd = COMMAND_TYPE(createTextureR);
    void* const p = allocateCommand(CommandBase::align(sizeof(Cmd)));
    new(p) Cmd(mDispatcher.createTexture_, TextureHandle(result), std::move(target), ...);
    return result;                                      // 立刻返回 Handle
}
```

#### 在 Dispatcher.h 中展开

```cpp
Execute draw2_;               // 函数指针
// 同步 API → 跳过（不入队，无需分派）
Execute createTexture_;       // 函数指针
```

#### 在 CommandStreamDispatcher.h 中展开

```cpp
// DECL_DRIVER_API → 生成静态分派方法
static void draw2(Driver& driver, CommandBase* base, intptr_t* next) {
    using Cmd = COMMAND_TYPE(draw2);
    ConcreteDriver& concreteDriver = static_cast<ConcreteDriver&>(driver);
    Cmd::execute(&ConcreteDriver::draw2, concreteDriver, base, next);
}

// DECL_DRIVER_API_RETURN → 分派 R 方法
static void createTexture(Driver& driver, CommandBase* base, intptr_t* next) {
    using Cmd = COMMAND_TYPE(createTextureR);
    ConcreteDriver& concreteDriver = static_cast<ConcreteDriver&>(driver);
    Cmd::execute(&ConcreteDriver::createTextureR, concreteDriver, base, next);
}
```

## 3. 类继承体系

```
Driver (纯接口，Driver.h)
  │  - 同步 API: pure virtual
  │  - 异步 API: 空函数体（占位，用于模板类型推导）
  │  - getDispatcher(): pure virtual
  │  - purge(): pure virtual
  │  - execute(): virtual（有默认实现）
  │
  └── DriverBase (基础实现，DriverBase.h)
        │  - purge(): final（回调队列处理）
        │  - scheduleCallback(): final（线程间回调调度）
        │  - debugCommandBegin/End(): override（调试支持）
        │  - ServiceThread: 用户回调执行线程
        │  - Hw* 结构体: 硬件资源句柄的基础定义
        │
        ├── OpenGLDriver    (opengl/OpenGLDriver.h)
        ├── VulkanDriver    (vulkan/VulkanDriver.h)
        ├── MetalDriver     (metal/MetalDriver.h)
        ├── WebGPUDriver    (webgpu/WebGPUDriver.h)
        └── NoopDriver      (noop/NoopDriver.h)
```

### Driver 基类的方法分类

| 方法 | 类型 | 说明 |
|------|------|------|
| `purge()` | pure virtual | 主线程调用，执行用户回调 |
| `scheduleCallback()` | pure virtual | 渲染线程调度回调 |
| `getShaderModel()` | pure virtual | 返回 Shader 模型 |
| `getShaderLanguages()` | pure virtual | 返回支持的 Shader 语言列表 |
| `getDispatcher()` | pure virtual | 返回 Dispatcher 函数指针表（仅初始化时调用一次） |
| `execute(fn)` | virtual（有默认实现） | 包装命令批次执行，默认直接调用 fn |
| `debugCommandBegin/End()` | pure virtual | 调试命令边界标记 |
| 所有 `DECL_DRIVER_API` 方法 | **非虚空函数** | 仅用于类型推导，不通过虚函数调用 |
| 所有 `DECL_DRIVER_API_SYNCHRONOUS` 方法 | pure virtual | 直接调用，需同步返回 |
| 所有 `DECL_DRIVER_API_RETURN` 的 `S` 方法 | pure virtual | 同步分配 Handle |
| 所有 `DECL_DRIVER_API_RETURN` 的 `R` 方法 | **非虚空函数** | 异步执行的实际创建逻辑 |

### DriverBase 提供的公共实现

`DriverBase` 继承自 `Driver`，为所有后端提供以下公共能力：

| 功能 | 说明 |
|------|------|
| **回调队列** (`purge()`) | 收集渲染线程产生的回调，在主线程执行 |
| **ServiceThread** | 独立线程执行用户回调，避免阻塞渲染线程 |
| **scheduleCallback** | 统一的回调调度机制 |
| **scheduleDestroy** | 安全释放 BufferDescriptor（带回调的走回调路径） |
| **debugCommand** | 调试命令的默认实现 |
| **Hw\* 结构体** | 所有硬件资源句柄的基础定义（HwTexture、HwBuffer 等） |

## 4. API 完整分类

### 帧控制

| API | 类型 | 说明 |
|-----|------|------|
| `tick` | 异步 | 心跳 |
| `beginFrame` | 异步 | 开始一帧 |
| `endFrame` | 异步 | 结束一帧 |
| `flush` | 异步 | 提交当前命令（如 glFlush） |
| `finish` | 异步 | 提交并等待完成 |
| `resetState` | 异步 | 重置状态跟踪 |
| `setPresentationTime` | 异步 | 设置呈现时间戳 |
| `setFrameScheduledCallback` | 异步 | 帧调度回调 |
| `setFrameCompletedCallback` | 异步 | 帧完成回调 |

### 资源创建（异步 + 返回 Handle）

| API | 返回类型 |
|-----|---------|
| `createVertexBufferInfo` | VertexBufferInfoHandle |
| `createVertexBuffer` | VertexBufferHandle |
| `createIndexBuffer` / `createIndexBufferAsync` | IndexBufferHandle |
| `createBufferObject` / `createBufferObjectAsync` | BufferObjectHandle |
| `createTexture` / `createTextureAsync` | TextureHandle |
| `createTextureView` / `createTextureViewSwizzle` | TextureHandle |
| `createTextureExternalImage` / `importTexture` | TextureHandle |
| `createRenderPrimitive` | RenderPrimitiveHandle |
| `createProgram` | ProgramHandle |
| `createRenderTarget` / `createDefaultRenderTarget` | RenderTargetHandle |
| `createFence` / `createSync` | FenceHandle / SyncHandle |
| `createSwapChain` / `createSwapChainHeadless` | SwapChainHandle |
| `createTimerQuery` | TimerQueryHandle |
| `createDescriptorSetLayout` / `createDescriptorSet` | DescriptorSetLayoutHandle / DescriptorSetHandle |
| `mapBuffer` | MemoryMappedBufferHandle |

### 资源销毁（异步）

`destroyVertexBuffer`、`destroyIndexBuffer`、`destroyTexture`、`destroyProgram`、`destroyRenderTarget`、`destroySwapChain`、`destroyFence`、`destroySync`、`destroyDescriptorSetLayout`、`destroyDescriptorSet`、`unmapBuffer` 等。

### 资源更新（异步）

| API | 说明 |
|-----|------|
| `setVertexBufferObject` | 绑定顶点缓冲区对象 |
| `updateIndexBuffer` / `updateBufferObject` | 更新缓冲区数据 |
| `updateBufferObjectUnsynchronized` | 无同步更新 |
| `update3DImage` | 更新纹理数据 |
| `generateMipmaps` | 生成 mipmap |
| `setExternalStream` | 绑定外部流 |
| `updateDescriptorSetBuffer` / `updateDescriptorSetTexture` | 更新描述符集 |
| `setPushConstant` | 设置推送常量 |

### 渲染状态（异步）

| API | 说明 |
|-----|------|
| `beginRenderPass` / `endRenderPass` | 渲染 Pass 边界 |
| `nextSubpass` | 下一个子 Pass |
| `bindPipeline` | 绑定管线状态 |
| `bindRenderPrimitive` | 绑定渲染图元 |
| `bindDescriptorSet` | 绑定描述符集 |
| `scissor` | 设置裁剪矩形 |

### 绘制（异步）

| API | 说明 |
|-----|------|
| `draw` | 完整绘制（含管线状态） |
| `draw2` | 轻量绘制（管线已绑定） |
| `dispatchCompute` | 计算分派 |
| `blit` / `resolve` | 拷贝/解析纹理 |

### 交换链（异步）

| API | 说明 |
|-----|------|
| `makeCurrent` | 设置当前交换链 |
| `commit` | 提交交换链 |

### 读回（异步）

`readPixels`、`readTexture`、`readBufferSubData`

### 查询（同步，直接返回）

`isTextureFormatSupported`、`isFrameBufferFetchSupported`、`getFeatureLevel`、`getMaxUniformBufferSize`、`getMaxTextureSize`、`getTimerQueryValue`、`isWorkaroundNeeded` 等。

### 调试（异步）

`insertEventMarker`、`pushGroupMarker`、`popGroupMarker`、`startCapture`、`stopCapture`

## 5. 三种 API 类型的执行路径对比

```
异步 API (DECL_DRIVER_API):
  主线程: CommandStream → allocate → placement new Cmd → 写入 CircularBuffer
  渲染线程: CommandBase::execute → Dispatcher → static_cast → ConcreteDriver::method()

同步 API (DECL_DRIVER_API_SYNCHRONOUS):
  主线程: CommandStream → apply(&Driver::method, mDriver, args) → 直接调用
  (不经过 CircularBuffer，不涉及渲染线程)

带返回值 API (DECL_DRIVER_API_RETURN):
  主线程: ① mDriver.methodS() → 同步拿 Handle
          ② allocate → placement new CmdR → 写入 CircularBuffer
          ③ return Handle
  渲染线程: CommandBase::execute → Dispatcher → ConcreteDriver::methodR(handle, args)
```

## 6. 后端实现者需要做什么

实现一个新后端（如 `FooDriver`）需要：

1. **继承 DriverBase**
   ```cpp
   class FooDriver : public DriverBase { ... };
   ```

2. **用 DriverAPI.inc 宏展开声明所有方法**
   ```cpp
   #define DECL_DRIVER_API(methodName, paramsDecl, params) \
       UTILS_ALWAYS_INLINE inline void methodName(paramsDecl);
   #define DECL_DRIVER_API_SYNCHRONOUS(RetType, methodName, paramsDecl, params) \
       RetType methodName(paramsDecl) override;
   #define DECL_DRIVER_API_RETURN(RetType, methodName, paramsDecl, params) \
       RetType methodName##S() noexcept override; \
       UTILS_ALWAYS_INLINE inline void methodName##R(RetType, paramsDecl);
   #include "private/backend/DriverAPI.inc"
   ```

3. **实现 getDispatcher()**
   ```cpp
   Dispatcher FooDriver::getDispatcher() const noexcept {
       return ConcreteDispatcher<FooDriver>::make();
   }
   ```

4. **在 .cpp 末尾显式实例化**
   ```cpp
   template class ConcreteDispatcher<FooDriver>;
   ```

5. **实现所有 API 方法的具体逻辑**（在 .cpp 中逐个实现）

6. **声明 ConcreteDispatcher 为友元**（使分派函数能调用 inline 方法）
   ```cpp
   template<typename T> friend class ConcreteDispatcher;
   ```

## 7. 关键文件索引

| 文件 | 角色 |
|------|------|
| `backend/include/private/backend/DriverAPI.inc` | **规范定义中心**：所有 API 的声明列表 |
| `backend/include/private/backend/Driver.h` | Driver 基类，用 DriverAPI.inc 生成虚函数/占位函数 |
| `backend/src/DriverBase.h` | DriverBase，提供公共实现 + Hw* 硬件句柄定义 |
| `backend/include/backend/DriverApiForward.h` | `using DriverApi = CommandStream` 类型别名 |
| `backend/include/private/backend/DriverApi.h` | `allocateFromCommandStream` 辅助函数 |
| `backend/include/private/backend/CommandStream.h` | 用 DriverAPI.inc 生成命令编码方法 |
| `backend/include/private/backend/Dispatcher.h` | 用 DriverAPI.inc 生成函数指针成员 |
| `backend/src/CommandStreamDispatcher.h` | 用 DriverAPI.inc 生成具体后端分派函数 |
| `backend/src/opengl/OpenGLDriver.h/.cpp` | OpenGL 后端实现 |
| `backend/src/vulkan/VulkanDriver.h/.cpp` | Vulkan 后端实现 |
| `backend/src/metal/MetalDriver.h/.mm` | Metal 后端实现 |
| `backend/src/webgpu/WebGPUDriver.h/.cpp` | WebGPU 后端实现 |
| `backend/src/noop/NoopDriver.h/.cpp` | 空实现后端（测试用） |
