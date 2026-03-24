# CommandStream.h

## 文件概述

定义了 Filament 后端的命令流系统，包括命令基类 `CommandBase`、命令类型模板 `CommandType`、自定义命令 `CustomCommand` 以及核心的 `CommandStream` 类。命令流是渲染线程与驱动线程之间通信的基础机制。

## 核心类

### CommandBase

所有命令的基类，包含一个函数指针用于执行命令。

| 方法 | 说明 |
|------|------|
| `align(v)` | 将大小对齐到 `max_align_t` |
| `execute(driver)` | 执行命令并返回下一条命令 |

**std::max_align_t 说明：**

`std::max_align_t` 是 C++ 标准库定义的类型，表示当前平台上所有标量类型所需的最大对齐要求。它的对齐值等于或大于所有基本类型（如 `int`、`double`、`long double`、指针等）的对齐要求。

**CommandBase::align() 函数的作用：**

将给定的大小对齐到 `std::max_align_t` 的边界，确保分配的内存满足最严格的对齐要求。这对于命令流中的内存分配很重要，因为：

1. **类型安全**：确保任何类型的对象都能正确地放置在分配的内存中
2. **性能优化**：对齐的内存访问通常更快
3. **平台兼容性**：某些架构（如 ARM）对未对齐访问有严格限制

**实现逻辑：**
```cpp
static constexpr size_t align(size_t size) noexcept {
    return (size + alignof(std::max_align_t) - 1) & ~(alignof(std::max_align_t) - 1);
}
```

这会将 `size` 向上舍入到 `std::max_align_t` 对齐的下一个倍数。例如，如果 `alignof(std::max_align_t) == 16`，那么 `align(13)` 会返回 16。

**CommandBase::execute() 函数返回值：**

返回值是 `CommandBase*`，即指向**下一条命令的指针**。

**具体实现逻辑：**

```cpp
CommandBase* execute(Driver& driver) {
    intptr_t next;
    mExecute(driver, this, &next);
    return reinterpret_cast<CommandBase*>(reinterpret_cast<intptr_t>(this) + next);
}
```

1. **执行当前命令**：调用 `mExecute` 函数指针执行当前命令的具体逻辑
2. **获取偏移量**：`mExecute` 通过输出参数 `next` 返回下一条命令相对于当前命令的字节偏移量
3. **计算下一条命令地址**：将当前命令的地址加上偏移量，得到下一条命令的地址

**命令链表结构：**

命令流通过链表结构组织，每个命令的 `execute()` 方法返回下一条命令的指针。当返回 `nullptr` 时，表示命令链表结束。

**在 CommandStream::execute() 中的使用：**

```cpp
CommandBase* base = static_cast<CommandBase*>(buffer);
while (UTILS_LIKELY(p)) {
    p = p->execute(d);  // 执行当前命令并获取下一条命令
}
```

这种设计允许命令流高效地遍历和执行所有命令，同时支持动态的命令链表结构。

**std::max_align_t 说明：**

`std::max_align_t` 是 C++ 标准库定义的类型，表示当前平台上所有标量类型所需的最大对齐要求。它的对齐值等于或大于所有基本类型（如 `int`、`double`、`long double`、指针等）的对齐要求。

**CommandBase::align() 函数的作用：**

将给定的大小对齐到 `std::max_align_t` 的边界，确保分配的内存满足最严格的对齐要求。这对于命令流中的内存分配很重要，因为：

1. **类型安全**：确保任何类型的对象都能正确地放置在分配的内存中
2. **性能优化**：对齐的内存访问通常更快
3. **平台兼容性**：某些架构（如 ARM）对未对齐访问有严格限制

**实现逻辑：**
```cpp
static constexpr size_t align(size_t size) noexcept {
    return (size + alignof(std::max_align_t) - 1) & ~(alignof(std::max_align_t) - 1);
}
```

这会将 `size` 向上舍入到 `std::max_align_t` 对齐的下一个倍数。例如，如果 `alignof(std::max_align_t) == 16`，那么 `align(13)` 会返回 16。

### CommandType\<void(Driver::*)(ARGS...)\>::Command

基于 Driver 方法签名的命令模板。使用 `std::tuple` 保存构造时传入的参数，执行时解包并调用对应的 Driver 方法。

**辅助模板函数：**

CommandStream 定义了三个辅助模板函数用于参数解包和成员函数调用：

#### 1. `invoke()`
```cpp
template<typename T, typename Type, typename D, typename ... ARGS>
constexpr decltype(auto) invoke(Type T::* m, D&& d, ARGS&& ... args) {
    static_assert(std::is_base_of<T, std::decay_t<D>>::value,
            "member function and object not related");
    return (std::forward<D>(d).*m)(std::forward<ARGS>(args)...);
}
```
**作用**：调用对象的成员函数
- `m`：成员函数指针
- `d`：对象引用
- `args`：参数列表
- 使用 `.*` 操作符调用成员函数，完美转发所有参数

#### 2. `trampoline()`
```cpp
template<typename M, typename D, typename T, std::size_t... I>
constexpr decltype(auto) trampoline(M&& m, D&& d, T&& t, std::index_sequence<I...>) {
    return invoke(std::forward<M>(m), std::forward<D>(d), std::get<I>(std::forward<T>(t))...);
}
```
**作用**：从 `std::tuple` 中提取参数并调用 `invoke()`
- 使用 `std::index_sequence<I...>` 和 `std::get<I>()` 将 tuple 展开
- 这是编译时的参数包展开技巧

#### 3. `apply()`
```cpp
template<typename M, typename D, typename T>
constexpr decltype(auto) apply(M&& m, D&& d, T&& t) {
    return trampoline(std::forward<M>(m), std::forward<D>(d), std::forward<T>(t),
            std::make_index_sequence< std::tuple_size<std::remove_reference_t<T>>::value >{});
}
```
**作用**：生成 `index_sequence` 并调用 `trampoline()`
- 自动生成从 0 到 tuple 大小的索引序列
- 这是用户调用的入口函数

**为什么不直接使用 `std::apply()`？**

可能的原因：

1. **C++ 标准版本兼容性**：`std::apply()` 是 C++17 引入的，如果项目需要支持 C++14 或更早版本，就需要自己实现

2. **性能优化**：自定义实现可能针对特定场景进行了优化，比如：
   - 更好的内联优化
   - 减少模板实例化开销
   - 针对成员函数指针的特殊处理

3. **编译器兼容性**：某些旧编译器可能对 `std::apply()` 的支持不完善

4. **代码自包含**：减少对标准库的依赖，便于移植和维护

**使用场景示例：**
```cpp
// CommandType::Command::execute() 中的使用
apply(std::forward<M>(method), std::forward<D>(driver), std::move(self->mArgs));
// 等价于：driver.method(std::get<0>(args), std::get<1>(args), ...)
```

这种设计允许命令对象将参数存储在 `std::tuple` 中，执行时自动解包并调用对应的 Driver 方法。

### CustomCommand

允许将 `std::function<void()>` 作为命令排队，效率低于原生 Driver API 命令。

**特点**：
- 封装 `std::function<void()>`，提供灵活性
- 可以执行任意 lambda 或函数对象
- **效率较低**：相比原生 Driver API 命令，有额外的类型擦除和函数调用开销

**使用场景**：
```cpp
void CommandStream::queueCommand(std::function<void()> command) {
    new(allocateCommand(CustomCommand::align(sizeof(CustomCommand))))
        CustomCommand(std::move(command));
}
```

### queueCommand() 方法

`queueCommand()` 的作用是**允许用户将任意的 lambda 函数或函数对象作为命令排队到命令流中**。

#### 函数签名
```cpp
void CommandStream::queueCommand(std::function<void()> command)
```

#### 实现
```cpp
void CommandStream::queueCommand(std::function<void()> command) {
    new(allocateCommand(CustomCommand::align(sizeof(CustomCommand))))
        CustomCommand(std::move(command));
}
```

#### 工作原理

1. **接收任意可调用对象**：接受 `std::function<void()>`，可以是 lambda、函数指针、函数对象等
2. **创建 CustomCommand**：将可调用对象封装到 `CustomCommand` 中
3. **放入环形队列**：通过 placement new 在环形队列中构造命令对象
4. **渲染线程执行**：在渲染线程上执行这个自定义命令

#### 使用场景

**1. 执行自定义逻辑**
```cpp
commandStream.queueCommand([]() {
    // 执行一些自定义操作
    doSomethingCustom();
});
```

**2. 调试和日志**
```cpp
commandStream.queueCommand([]() {
    LOG(INFO) << "Command executed at frame " << frameCount;
});
```

**3. 资源管理**
```cpp
commandStream.queueCommand([resource]() {
    // 在渲染线程上释放资源
    releaseResource(resource);
});
```

**4. 条件执行**
```cpp
commandStream.queueCommand([condition]() {
    if (condition) {
        // 根据条件执行不同逻辑
    }
});
```

#### 与原生 Driver API 的对比

| 特性 | Driver API (CommandType) | queueCommand (CustomCommand) |
|------|--------------------------|------------------------------|
| **类型安全** | ✅ 编译时检查 | ⚠️ 运行时检查 |
| **性能** | ⭐⭐⭐⭐⭐ 高效 | ⭐⭐ 较低（类型擦除开销） |
| **灵活性** | ⭐⭐⭐ 需预定义 | ⭐⭐⭐⭐⭐ 任意代码 |
| **使用场景** | 标准图形 API 调用 | 自定义逻辑、调试、扩展 |

#### 性能考虑

`queueCommand()` 的性能开销主要来自：
1. **类型擦除**：`std::function` 的类型擦除开销
2. **虚函数调用**：`std::function` 内部的虚函数调用
3. **内存分配**：可能涉及堆内存分配（取决于 lambda 的捕获）

因此，**对于高频调用的标准图形 API，应该使用原生 Driver API**，而 `queueCommand()` 应该用于低频的、需要灵活性的场景。

#### 总结

`queueCommand()` 提供了一个**扩展点**，允许用户在不修改 CommandStream 源码的情况下，向命令流中插入自定义逻辑。这是一个权衡：牺牲了一些性能，换取了极大的灵活性。

### NoopCommand

空操作命令，用于跳转到指定位置（用于内存分配的填充）。

**特点**：
- 不执行任何实际操作，只返回下一条命令的地址
- 用于内存分配时的填充，确保对齐
- 存储相对于当前命令的偏移量

**使用场景**：
```cpp
// 在 CommandStream::allocate() 中用于填充和对齐
void* CommandStream::allocate(size_t size, size_t alignment) noexcept {
    const size_t s = CustomCommand::align(sizeof(NoopCommand) + size + alignment - 1);
    char* const p = (char *)allocateCommand(s);
    new(p) NoopCommand(p + s);  // 跳转到分配区域的末尾
    // ... 计算用户数据指针
}
```

## CommandBase 派生类对比

| 派生类 | 用途 | 效率 | 灵活性 |
|--------|------|------|--------|
| CommandType::Command | Driver API 调用 | ⭐⭐⭐⭐⭐ 高 | ⭐⭐⭐ 中（需预定义） |
| CustomCommand | 自定义 lambda | ⭐⭐ 低 | ⭐⭐⭐⭐⭐ 高 |
| NoopCommand | 内存填充/跳转 | ⭐⭐⭐⭐⭐ 高 | ⭐ 低（专用） |

**设计意图**：
- CommandType::Command 是主要的命令类型，用于所有 Driver API 调用
- CustomCommand 提供扩展性，允许用户插入自定义逻辑
- NoopCommand 是内部使用的辅助命令，用于内存管理

### CommandStream

核心命令流类，也即 `DriverApi`。

**CommandStream 调用图形 API 的流程：**

**核心机制**：CommandStream 将所有 Driver API 调用序列化为命令对象，并将这些命令放入环形队列（CircularBuffer）中。

#### 具体流程

1. **应用线程调用 Driver API**
   ```cpp
   // 例如：commandStream.setScissor(rect)
   // 宏展开为：
   inline void setScissor(Scissor rect) noexcept {
       DEBUG_COMMAND_BEGIN(setScissor, false, rect);
       using Cmd = COMMAND_TYPE(setScissor);
       void* const p = allocateCommand(CommandBase::align(sizeof(Cmd)));
       new(p) Cmd(mDispatcher.setScissor_, std::move(rect));
       DEBUG_COMMAND_END(setScissor, false);
   }
   ```

2. **分配命令内存**
   ```cpp
   void* allocateCommand(size_t const size) noexcept {
       assert_invariant(utils::ThreadUtils::isThisThread(mThreadId));
       return mCurrentBuffer.allocate(size);  // 从环形队列分配
   }
   ```

3. **构造命令对象**
   - 使用 placement new 在分配的内存上构造命令对象
   - 命令对象包含参数（存储在 `std::tuple` 中）和执行函数指针

4. **渲染线程执行命令**
   ```cpp
   void CommandStream::execute(void* buffer) {
       CommandBase* p = static_cast<CommandBase*>(buffer);
       while (UTILS_LIKELY(p)) {
           p = p->execute(mDriver);  // 执行命令并获取下一条
       }
   }
   ```

#### 架构优势

| 特性 | 说明 |
|------|------|
| **线程安全** | 应用线程写入命令，渲染线程执行命令，通过环形队列解耦 |
| **零拷贝** | 命令对象直接在环形队列中构造，无需额外拷贝 |
| **高效** | 编译时类型检查，零运行时开销 |
| **批量执行** | 渲染线程可以批量执行多个命令，减少上下文切换 |

#### 环形队列的作用

```cpp
class CommandStream {
    CircularBuffer& UTILS_RESTRICT mCurrentBuffer;  // 环形队列引用
    // ...
};
```

- **存储命令对象**：所有命令都存储在环形队列中
- **自动回收**：命令执行完毕后，内存自动回收
- **循环使用**：环形队列可以循环使用，避免频繁分配释放

**总结**：CommandStream 调用图形 API，实际上就是把 command 放到环形队列中，然后在渲染线程上批量执行这些命令。

## 主要 API (CommandStream)

| 方法 | 说明 |
|------|------|
| 所有 Driver API 方法 | 通过宏展开自 `DriverAPI.inc`，将调用序列化为命令 |
| `execute(buffer)` | 执行命令缓冲区中的所有命令 |
| `queueCommand(command)` | 排队一个 lambda 命令 |
| `allocate(size, alignment)` | 从命令流分配内存（命令缓冲区处理后自动释放） |
| `allocatePod<T>(count, alignment)` | 分配可平凡析构对象数组 |
| `debugThreading()` | 调试用，记录当前线程 ID |

## 宏定义

- `COMMAND_TYPE(method)` - 将 Driver 方法转为对应的 Command 类型
- `DECL_DRIVER_API` / `DECL_DRIVER_API_SYNCHRONOUS` / `DECL_DRIVER_API_RETURN` - 展开 Driver API 声明

## 依赖关系

- `private/backend/CircularBuffer.h` - 底层存储
- `private/backend/Dispatcher.h` - 命令分发器
- `private/backend/Driver.h` - 驱动接口
- `backend/BufferDescriptor.h` / `backend/Handle.h` / `backend/PipelineState.h` 等 - 命令参数类型

---

# CommandStream 架构全景：CommandStream × CommandBufferQueue × CircularBuffer

> 仅阅读 `CommandStream.h/.cpp` 的代码，无法看出 `CommandBufferQueue` 的存在——
> CommandStream 只知道自己往一块 `CircularBuffer` 里写命令。
> 把命令"切片"并交给渲染线程的工作，完全由 `CommandBufferQueue` 在上层完成。
> 本节把三者的关系和协作流程一次讲清楚。

## 1. 三个组件的角色

| 组件 | 角色 | 一句话 |
|------|------|--------|
| **CircularBuffer** | 底层内存 | 一块用 mmap 实现的环形缓冲区，只提供 `allocate()` 和 `getBuffer()` |
| **CommandStream** (DriverApi) | 命令编码器 | 把每一次 `draw()` / `bindTexture()` 等调用序列化为 Command 对象写入 CircularBuffer |
| **CommandBufferQueue** | 生产者-消费者队列 | 持有 CircularBuffer，负责 flush / wait / release 三步交接，并实现背压和暂停 |

三者是 **分层** 关系，不是平级关系：

```
┌─────────────────────────────────────────────────────────────────┐
│  上层代码 (Renderer / FEngine)                                    │
│    engine.getDriverApi().bindPipeline(...)                       │
│    engine.flush()                                                │
└─────────────┬──────────────────────────────────┬────────────────┘
              │ 写命令                            │ flush()
              ▼                                   ▼
┌──────────────────────┐            ┌──────────────────────────────┐
│    CommandStream      │            │    CommandBufferQueue         │
│  (命令编码器)          │◄──引用────│  (生产者-消费者队列)            │
│  持有 CircularBuffer& │            │  持有 CircularBuffer          │
└──────────┬───────────┘            └──────────────┬───────────────┘
           │ allocate()                             │ 拥有
           ▼                                        ▼
        ┌──────────────────────────────────────────────┐
        │              CircularBuffer                    │
        │  (mmap 环形缓冲区，真正的内存)                    │
        └──────────────────────────────────────────────┘
```

**关键：CommandStream 和 CommandBufferQueue 操作的是同一块 CircularBuffer。**
- `CommandBufferQueue` **拥有** CircularBuffer（成员变量 `mCircularBuffer`）
- `CommandStream` **引用** CircularBuffer（构造时传入 `CircularBuffer&`）

## 2. 所有权关系：FEngine 如何创建和持有这些对象

```cpp
// Engine.h:666-667
class FEngine {
    backend::CommandBufferQueue mCommandBufferQueue;          // 成员变量，拥有
    std::aligned_storage<sizeof(DriverApi)>::type mDriverApiStorage; // 手动管理生命周期
};
```

### 创建时序

**Step 1 — 构造 CommandBufferQueue（FEngine 构造函数）**

```cpp
// Engine.cpp:272-275
FEngine::FEngine(Builder const& builder)
    : ...
      mCommandBufferQueue(
          builder->mConfig.minCommandBufferSizeMB * MiB,   // requiredSize：flush 后保证的可用空间
          builder->mConfig.commandBufferSizeMB * MiB,      // bufferSize：CircularBuffer 总大小
          builder->mPaused),
      ...
```
此时 `CommandBufferQueue` 内部已经创建好了 `CircularBuffer`。

**Step 2 — 构造 CommandStream（FEngine::init()）**

```cpp
// Engine.cpp:343-344
void FEngine::init() {
    assert_invariant( intptr_t(&mDriverApiStorage) % alignof(DriverApi) == 0 );
    ::new(&mDriverApiStorage) DriverApi(*mDriver, mCommandBufferQueue.getCircularBuffer());
    //                                  ^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    //                                  Driver&   CommandBufferQueue 内部的 CircularBuffer&
```
`CommandStream` 通过 placement new 构造在预分配的 `mDriverApiStorage` 中。
它拿到的 `CircularBuffer&` 来自 `mCommandBufferQueue.getCircularBuffer()`。

**Step 3 — 销毁（FEngine::shutdown()）**

```cpp
// Engine.cpp:720
std::destroy_at(std::launder(reinterpret_cast<DriverApi*>(&mDriverApiStorage)));
```
先手动析构 CommandStream，之后 `mCommandBufferQueue` 随 `FEngine` 析构自动销毁。

### 获取 CommandStream 引用

```cpp
// Engine.h:163-165
DriverApi& getDriverApi() noexcept {
    return *std::launder(reinterpret_cast<DriverApi*>(&mDriverApiStorage));
}
```
其中 `DriverApi` 就是 `CommandStream` 的类型别名（见 `DriverApiForward.h:24`）。

## 3. 上层如何使用

```cpp
// 典型用法：
DriverApi& driver = engine.getDriverApi();  // 获取 CommandStream 引用

// 调用各种图形 API —— 实际上是往 CircularBuffer 写命令
driver.beginFrame(monotonic_clock_ns, refreshIntervalNs, frameId);
driver.beginRenderPass(rth, params);
driver.bindPipeline(state);
driver.draw2(indexOffset, indexCount, instanceCount);
driver.endRenderPass();
driver.commit(swapChain);
driver.endFrame(frameId);

// 刷新命令缓冲区 —— 把写好的命令交给渲染线程
engine.flush();  // 内部调用 mCommandBufferQueue.flush()
```

上层代码 **只** 和 `CommandStream`（DriverApi）打交道，不直接接触 `CommandBufferQueue`。
`CommandBufferQueue` 的 `flush()` / `waitForCommands()` / `releaseBuffer()` 由 `FEngine` 在内部调用。

## 4. CommandStream 命令编码机制详解

### 4.1 DriverAPI.inc 宏生成方法

`DriverAPI.inc` 不是普通头文件——它 **没有 include guard**，被设计为多次包含。
每次包含前，调用方定义三个宏，`DriverAPI.inc` 就会用这些宏"遍历"所有 Driver API：

```cpp
// 在 CommandStream.h 中定义宏（第 223-250 行），然后包含
#define DECL_DRIVER_API(methodName, paramsDecl, params)              ...
#define DECL_DRIVER_API_SYNCHRONOUS(RetType, methodName, paramsDecl, params) ...
#define DECL_DRIVER_API_RETURN(RetType, methodName, paramsDecl, params)      ...
#include "DriverAPI.inc"
```

这样 `CommandStream` 就自动获得了所有 Driver API 的方法，如 `beginFrame()`、`draw2()`、`createTexture()` 等。

### 4.2 三种命令类型

#### (1) 异步命令 `DECL_DRIVER_API` — 纯入队，不等待

```cpp
// CommandStream.h:223-230
#define DECL_DRIVER_API(methodName, paramsDecl, params)                    \
    inline void methodName(paramsDecl) noexcept {                         \
        using Cmd = COMMAND_TYPE(methodName);                             \
        void* const p = allocateCommand(CommandBase::align(sizeof(Cmd))); \
        new(p) Cmd(mDispatcher.methodName##_, APPLY(std::move, params));  \
    }
```

- 在 CircularBuffer 中分配空间
- 用 placement new 构造 Command 对象，把参数 move 进 `std::tuple`
- **立即返回**，不阻塞调用线程
- 示例：`draw2`、`beginRenderPass`、`destroyTexture`

#### (2) 同步命令 `DECL_DRIVER_API_SYNCHRONOUS` — 直接调用 Driver，不入队

```cpp
// CommandStream.h:232-239
#define DECL_DRIVER_API_SYNCHRONOUS(RetType, methodName, paramsDecl, params) \
    inline RetType methodName(paramsDecl) noexcept {                        \
        return apply(&Driver::methodName, mDriver, std::forward_as_tuple(params)); \
    }
```

- **直接调用** `mDriver.methodName(params)`，在当前线程立即执行
- 不经过 CircularBuffer，不入队
- 用于查询类 API：`isTextureFormatSupported`、`getFeatureLevel`、`getMaxUniformBufferSize` 等
- 可以有返回值

#### (3) 带返回值的异步命令 `DECL_DRIVER_API_RETURN` — 先拿 Handle，再入队

```cpp
// CommandStream.h:241-250
#define DECL_DRIVER_API_RETURN(RetType, methodName, paramsDecl, params)    \
    inline RetType methodName(paramsDecl) noexcept {                      \
        RetType result = mDriver.methodName##S();                         \
        using Cmd = COMMAND_TYPE(methodName##R);                          \
        void* const p = allocateCommand(CommandBase::align(sizeof(Cmd))); \
        new(p) Cmd(mDispatcher.methodName##_, RetType(result), APPLY(std::move, params)); \
        return result;                                                    \
    }
```

- **两步走**：
  1. 同步调用 `mDriver.methodNameS()` 分配一个 Handle（轻量操作）
  2. 异步入队 `methodNameR` 命令，携带 Handle 和参数
- 调用者立刻拿到 Handle，可以继续使用（Handle 是前向引用）
- 渲染线程执行时才真正创建 GPU 资源
- 示例：`createTexture`、`createVertexBuffer`、`createProgram`

### 4.3 Command 模板类结构

```
CommandBase (基类)
  ├── mExecute: Execute 函数指针
  │
  └── CommandType<void(Driver::*)(ARGS...)>::Command<&Driver::method> (具体命令)
        ├── mArgs: std::tuple<ARGS...>   保存的参数
        │
        └── static execute(method, driver, base, next)
              1. 计算 *next = align(sizeof(Command))   // 下一条命令的偏移
              2. apply(method, driver, move(self->mArgs)) // 解包 tuple 并调用 Driver 方法
              3. self->~Command()                        // 析构命令对象
```

### 4.4 Dispatcher 模式

```cpp
// CommandStream 持有 Dispatcher 副本（不是指针，减少一次间接寻址）
Dispatcher mDispatcher;
```

`Dispatcher` 是一张函数指针表，每个 Driver API 对应一个条目（如 `mDispatcher.draw2_`）。
这些函数指针在 `Command::execute()` 的静态方法中被使用，作为模板参数的"桥梁"将类型信息转化为运行时调用。

## 5. CommandBufferQueue 生产者-消费者机制

### 5.1 数据结构总览

```cpp
// CommandBufferQueue.h
class CommandBufferQueue {
    const size_t mRequiredSize;                        // flush 后保证的最小可用空间
    CircularBuffer mCircularBuffer;                    // 拥有的环形缓冲区

    mutable utils::Mutex mLock;                        // 保护以下所有字段
    mutable utils::Condition mCondition;               // 双重用途条件变量
    mutable std::vector<Range> mCommandBuffersToExecute; // 待执行的命令切片队列
    size_t mFreeSpace = 0;                             // 逻辑可用空间计数器
    size_t mHighWatermark = 0;                         // 调试用：最高水位
    uint32_t mExitRequested = 0;                       // 退出标志
    bool mPaused = false;                              // 暂停标志
};
```

`Range` 是一对指针 `{begin, end}`，表示 CircularBuffer 中一段已写入的命令数据。

### 5.2 flush() / waitForCommands() / releaseBuffer() 三步交接详解

这三个方法构成完整的生产者-消费者协议：

```
  主线程 (生产者)                          渲染线程 (消费者)
  ─────────────────                       ─────────────────
       │                                       │
  ① flush()                                    │
       │  加终止命令 NoopCommand(nullptr)        │
       │  getBuffer() 取出 {begin, end}         │
       │  lock → mFreeSpace -= used            │
       │       → push_back({begin,end})        │
       │       → notify_one ──────────────► ② waitForCommands()
       │  若 mFreeSpace < requiredSize          │   lock → wait 直到有数据
       │       → wait (背压阻塞) ◄──────────    │   return move(mCommandBuffersToExecute)
       │                                │       │
       │                                │  ③ 逐个执行 buffer
       │                                │       │   driver.execute(item.begin)
       │                                │       │
       │                                └── ④ releaseBuffer(item)
       │                                        │   lock → mFreeSpace += used
       │   ◄──── notify_one ────────────────────│   → notify_one (唤醒背压)
       │  (背压解除，继续写命令)                     │
```

#### flush() 详解 (生产者端)

```cpp
// CommandBufferQueue.cpp:82-143
void CommandBufferQueue::flush() {
    CircularBuffer& circularBuffer = mCircularBuffer;
    if (circularBuffer.empty()) return;                          // 无数据则跳过

    // 1. 写入终止标记：NoopCommand(nullptr) → 执行链到此结束
    new(circularBuffer.allocate(sizeof(NoopCommand))) NoopCommand(nullptr);

    // 2. 从 CircularBuffer 取出本次写入的范围
    auto const [begin, end] = circularBuffer.getBuffer();        // 重置 head/tail

    size_t const used = distance(begin, end);

    // 3. 加锁，更新逻辑可用空间
    std::unique_lock lock(mLock);
    FILAMENT_CHECK_POSTCONDITION(used <= mFreeSpace) << "overflow";
    mFreeSpace -= used;                                          // 扣减可用空间
    mCommandBuffersToExecute.push_back({ begin, end });          // 入队
    mCondition.notify_one();                                     // 唤醒消费者

    // 4. 背压：若剩余空间不足，等待消费者释放
    if (mFreeSpace < mRequiredSize) {
        mCondition.wait(lock, [this, requiredSize]() {
            return mFreeSpace >= requiredSize;
        });
    }
}
```

#### waitForCommands() 详解 (消费者端)

```cpp
// CommandBufferQueue.cpp:145-154
std::vector<Range> CommandBufferQueue::waitForCommands() const {
    std::unique_lock lock(mLock);
    while ((mCommandBuffersToExecute.empty() || mPaused) && !mExitRequested) {
        mCondition.wait(lock);                                   // 阻塞等待命令到来
    }
    return std::move(mCommandBuffersToExecute);                  // 移出所有待执行切片
}
```

#### releaseBuffer() 详解 (消费者端)

```cpp
// CommandBufferQueue.cpp:156-162
void CommandBufferQueue::releaseBuffer(Range const& buffer) {
    size_t const used = distance(buffer.begin, buffer.end);
    std::lock_guard const lock(mLock);
    mFreeSpace += used;                                          // 归还空间
    mCondition.notify_one();                                     // 唤醒可能阻塞的生产者
}
```

### 5.3 背压机制 — mFreeSpace 逻辑计数器

`mFreeSpace` 是 **逻辑** 可用空间，不是 CircularBuffer 的物理可用空间：

| 事件 | mFreeSpace 变化 |
|------|----------------|
| 构造时 | 初始化为 `mCircularBuffer.size()` 即缓冲区总大小 |
| `flush()` | `-= used`（生产者写了多少就扣多少） |
| `releaseBuffer()` | `+= used`（消费者执行完就归还） |

**为什么需要逻辑计数器？**

CircularBuffer 的 `getBuffer()` 会立即重置 head/tail，此后 CircularBuffer 从物理上看又是"空的"，可以继续写入。但如果消费者还没执行完上一批命令，那段内存还不能被覆盖。`mFreeSpace` 跟踪的就是"消费者已释放、可安全写入"的空间总量。

**背压触发条件：**

```cpp
if (mFreeSpace < mRequiredSize) {
    // 阻塞，等待消费者释放足够空间
    mCondition.wait(lock, [this, requiredSize]() {
        return mFreeSpace >= requiredSize;
    });
}
```

`mRequiredSize` 是构造时传入的 `minCommandBufferSizeMB`，保证 flush 返回后至少有这么多空间可写。

### 5.4 条件变量双重用途

`mCondition` 同时服务于两个方向的等待：

| 等待方 | 等待条件 | 被谁唤醒 |
|--------|---------|---------|
| 生产者（主线程 flush） | `mFreeSpace >= mRequiredSize` | 消费者 `releaseBuffer()` 中的 `notify_one` |
| 消费者（渲染线程 waitForCommands） | `!mCommandBuffersToExecute.empty() && !mPaused` | 生产者 `flush()` 中的 `notify_one` |

能共用一个条件变量是因为两者的等待/唤醒不会冲突：生产者写完唤醒消费者，消费者执行完唤醒生产者。

### 5.5 暂停 / 退出机制

**暂停：**
```cpp
void CommandBufferQueue::setPaused(bool paused) {
    std::lock_guard const lock(mLock);
    mPaused = paused;
    if (!paused) mCondition.notify_one();  // 解除暂停时唤醒消费者
}
```
暂停时 `waitForCommands()` 即使有命令也不会返回。但 `flush()` 中有检查：如果暂停且空间不足会直接 panic（否则会死锁）。

**退出：**
```cpp
void CommandBufferQueue::requestExit() {
    std::lock_guard const lock(mLock);
    mExitRequested = EXIT_REQUESTED;       // 魔数 0x31415926
    mCondition.notify_one();               // 唤醒消费者
}
```
`waitForCommands()` 检测到 `mExitRequested` 后返回空 vector，渲染线程退出循环。

## 6. FEngine 中的调用位置

### 6.1 flushCommandBuffer() — 生产者端

```cpp
// Engine.cpp:955-958
void FEngine::flushCommandBuffer(CommandBufferQueue& commandBufferQueue) const {
    getDriver().purge();              // 先处理 Driver 的回调队列
    commandBufferQueue.flush();       // 调用 CommandBufferQueue::flush()
}
```

被调用的地方：
- `FEngine::flush()` (`Engine.cpp:820`) — 外部触发的刷新
- `FEngine::shutdown()` (`Engine.cpp:697`) — 关闭前最后一次刷新

`FEngine::flush()` 还额外处理单线程模式：
```cpp
// Engine.cpp:820-829
void FEngine::flush() {
    flushCommandBuffer(mCommandBufferQueue);
    if constexpr (!UTILS_HAS_THREADING) {
        execute();  // 单线程模式下手动执行
    }
}
```

### 6.2 execute() — 消费者端

```cpp
// Engine.cpp:1589-1606
bool FEngine::execute() {
    // 1. 等待命令到来
    auto const buffers = mCommandBufferQueue.waitForCommands();
    if (UTILS_UNLIKELY(buffers.empty())) {
        return false;  // 空 = exitRequested，退出循环
    }

    // 2. 逐个执行命令缓冲区
    auto& driver = getDriverApi();
    for (auto& item : buffers) {
        if (UTILS_LIKELY(item.begin)) {
            driver.execute(item.begin);              // CommandStream::execute() 遍历命令链
            mCommandBufferQueue.releaseBuffer(item); // 释放空间，可能唤醒生产者
        }
    }
    return true;
}
```

被调用的地方：
- `FEngine::loop()` (`Engine.cpp:933-936`) — 渲染线程主循环
  ```cpp
  while (true) {
      if (!execute()) break;  // execute 返回 false 时退出
  }
  ```
- `FEngine::flush()` — 单线程模式下手动调用
- `FEngine::create()` (`Engine.cpp:199`) — 单线程模式初始化

## 7. 完整时序图：从上层 API 调用到 GPU 命令执行

```
 主线程 (UI/App Thread)                          渲染线程 (FEngine::loop)
 ════════════════════                            ═══════════════════════
                                                  │
 ┌─ FRenderer::renderFrame() ───────────┐         │ CommandBufferQueue::waitForCommands()
 │                                       │         │   └─ mCondition.wait() ... 阻塞等待
 │  DriverApi& driver = getDriverApi()  │         │
 │                                       │         │
 │  driver.beginFrame(...)              │         │
 │    └─ DECL_DRIVER_API 宏展开：        │         │
 │       1. allocateCommand(sizeof(Cmd))│         │
 │          └─ CircularBuffer::allocate()         │
 │       2. new(p) Cmd(dispatcher, args)│         │
 │                                       │         │
 │  driver.beginRenderPass(rth, params) │         │
 │  driver.bindPipeline(state)          │         │
 │  driver.draw2(offset, count, inst)   │         │
 │  driver.endRenderPass()              │         │
 │  driver.commit(swapChain)            │         │
 │  driver.endFrame(frameId)            │         │
 │                                       │         │
 └───────────────────────────────────────┘         │
                                                   │
 FEngine::flush()                                  │
   └─ flushCommandBuffer(mCommandBufferQueue)      │
       └─ CommandBufferQueue::flush()              │
           1. new NoopCommand(nullptr)  // 终止符   │
           2. getBuffer() → {begin, end}           │
           3. lock:                                │
              mFreeSpace -= used                   │
              mCommandBuffersToExecute.push_back() │
              notify_one ─────────────────────────►│
           4. if mFreeSpace < required:            │ waitForCommands() 被唤醒
              wait (背压) ◄────────────┐           │   return move(buffers)
                                       │           │
                                       │           │ for (item : buffers):
                                       │           │   CommandStream::execute(item.begin)
                                       │           │     └─ while(p) p = p->execute(driver)
                                       │           │         └─ Command::execute()
                                       │           │             1. apply(method, driver, args)
                                       │           │                └─ Driver::draw2(...)
                                       │           │                    └─ glDrawElements(...)
                                       │           │             2. self->~Command()
                                       │           │
                                       │           │   CommandBufferQueue::releaseBuffer(item)
                                       └───────────│     mFreeSpace += used
                                                   │     notify_one → 唤醒背压
                                                   │
                                                   │ → 回到 waitForCommands() 等待下一批
```

## 8. 关键文件索引

| 文件 | 角色 |
|------|------|
| `filament/backend/include/private/backend/CommandStream.h` | CommandStream 类定义、三种命令宏、CommandBase/CommandType/CustomCommand/NoopCommand |
| `filament/backend/src/CommandStream.cpp` | CommandStream 构造函数、`execute()`、`queueCommand()`、debug 日志 |
| `filament/backend/include/private/backend/CommandBufferQueue.h` | CommandBufferQueue 类定义、Range 结构体 |
| `filament/backend/src/CommandBufferQueue.cpp` | flush/waitForCommands/releaseBuffer 实现、背压逻辑 |
| `filament/backend/include/private/backend/CircularBuffer.h` | CircularBuffer 类定义、allocate/getBuffer |
| `filament/backend/include/private/backend/DriverAPI.inc` | 所有 Driver API 的声明列表，被多次 include |
| `filament/backend/include/backend/DriverApiForward.h` | `using DriverApi = CommandStream` 类型别名 |
| `filament/src/details/Engine.h` | FEngine 持有 `mCommandBufferQueue` 和 `mDriverApiStorage` |
| `filament/src/details/Engine.cpp` | `FEngine::init()` 创建 CommandStream、`loop()` / `execute()` 消费者循环、`flush()` 生产者刷新 |
