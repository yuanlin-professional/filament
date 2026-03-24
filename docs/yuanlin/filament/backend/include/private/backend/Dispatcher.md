# Dispatcher.h

## 文件概述

定义了 `Dispatcher` 类，一个仅包含函数指针的数据结构。每个函数指针对应一个 Driver 方法的命令解包和执行代码。在初始化时填充，之后在命令插入流时从 Dispatcher 复制到 CommandBase。

## 核心问题

Dispatcher 解决的核心问题是：**命令编码时（主线程）不知道具体的 Driver 子类是什么（OpenGL? Vulkan? Metal?），但执行时需要调用具体子类的方法。**

## 核心类

### Dispatcher

命令分发表，为每个 Driver API 方法存储一个执行函数指针。

**类型定义**:
- `Execute` = `void (*)(Driver& driver, CommandBase* self, intptr_t* next)` - 命令执行函数签名

**成员**:
通过宏展开 `DriverAPI.inc` 生成，每个非同步 Driver 方法对应一个 `Execute` 类型的成员变量（命名为 `methodName_`）。同步 API 不需要，因为它们直接调用不入队。

```cpp
// Dispatcher.h
class Dispatcher {
    using Execute = void (*)(Driver& driver, CommandBase* self, intptr_t* next);

    Execute beginFrame_;      // 函数指针
    Execute draw2_;           // 函数指针
    Execute createTexture_;   // 函数指针
    // ... 每个异步 Driver API 一个
};
```

### ConcreteDispatcher\<ConcreteDriver\>

模板类，位于 `CommandStreamDispatcher.h`。负责为具体的 Driver 子类（如 `OpenGLDriver`、`VulkanDriver`）生成 Dispatcher 函数指针表。

## 工作流程

### 1. 初始化时 — 填充函数指针（`ConcreteDispatcher<T>::make()`）

```cpp
// CommandStreamDispatcher.h:76-88
template<typename ConcreteDriver>  // 如 OpenGLDriver, VulkanDriver
Dispatcher ConcreteDispatcher<ConcreteDriver>::make() noexcept {
    Dispatcher dispatcher;
    // 宏展开为：
    dispatcher.beginFrame_ = &ConcreteDispatcher::beginFrame;
    dispatcher.draw2_      = &ConcreteDispatcher::draw2;
    dispatcher.createTexture_ = &ConcreteDispatcher::createTexture;
    // ...
    return dispatcher;
}
```

### 2. 每个函数指针指向的静态方法 — 做向下转型 + 调用

```cpp
// CommandStreamDispatcher.h:57-63
static void draw2(Driver& driver, CommandBase* base, intptr_t* next) {
    using Cmd = COMMAND_TYPE(draw2);
    ConcreteDriver& concreteDriver = static_cast<ConcreteDriver&>(driver);  // 向下转型
    Cmd::execute(&ConcreteDriver::draw2, concreteDriver, base, next);       // 调用具体实现
}
```

### 3. 命令编码时 — 把函数指针存入 Command 对象

```cpp
// CommandStream.h DECL_DRIVER_API 宏展开：
new(p) Cmd(mDispatcher.draw2_, std::move(args));
//         ^^^^^^^^^^^^^^^^^^
//         从 Dispatcher 取出函数指针，存入 CommandBase::mExecute
```

### 4. 命令执行时 — 通过函数指针回调

```cpp
// CommandBase::execute()
mExecute(driver, this, &next);  // 调用的就是上面的静态方法
```

## 为什么不用虚函数？

这个设计本质上等效于虚函数表，但有几个优势：

| 对比 | 虚函数 | Dispatcher 方案 |
|------|--------|----------------|
| 间接寻址次数 | 2 次（对象→vptr→vtable→函数） | 1 次（Command 对象直接存了函数指针） |
| 数据局部性 | vtable 可能在远处的内存 | CommandStream 持有 Dispatcher 副本，热缓存 |
| 每个 Command 的开销 | 需要 vptr（8 字节） | 同样存 mExecute（8 字节），但省去了 vtable 查找 |
| 编译时优化 | 编译器难以 devirtualize | 静态方法 + 模板，编译器更容易内联 |

CommandStream 特意持有 Dispatcher 的 **副本** 而非指针：

```cpp
// CommandStream.h:297
Dispatcher mDispatcher;  // 副本，不是指针
```

注释也说了："We use a copy of Dispatcher (instead of a pointer) because this removes one dereference when executing driver commands."

## ConcreteDispatcher 何时绑定具体 Driver

### 触发点：CommandStream 构造

```cpp
// CommandStream.cpp:73-76
CommandStream::CommandStream(Driver& driver, CircularBuffer& buffer) noexcept
    : mDriver(driver),
      mCurrentBuffer(buffer),
      mDispatcher(driver.getDispatcher())   // ← 这里通过虚函数拿到具体后端的 Dispatcher
```

### 每个后端各自实现 `getDispatcher()`

`getDispatcher()` 是 `Driver` 基类的纯虚函数（`Driver.h:97`），每个后端必须实现：

```cpp
// Driver.h:97
virtual Dispatcher getDispatcher() const noexcept = 0;

// OpenGLDriver.cpp:344-345
Dispatcher OpenGLDriver::getDispatcher() const noexcept {
    auto dispatcher = ConcreteDispatcher<OpenGLDriver>::make();
    return dispatcher;
}

// VulkanDriver.cpp:237-238
Dispatcher VulkanDriver::getDispatcher() const noexcept {
    return ConcreteDispatcher<VulkanDriver>::make();
}

// MetalDriver.mm:108-109
Dispatcher MetalDriver::getDispatcher() const noexcept {
    return ConcreteDispatcher<MetalDriver>::make();
}

// NoopDriver.cpp:41-42
Dispatcher NoopDriver::getDispatcher() const noexcept {
    return ConcreteDispatcher<NoopDriver>::make();
}

// WebGPUDriver.cpp:124-125
Dispatcher WebGPUDriver::getDispatcher() const noexcept {
    return ConcreteDispatcher<WebGPUDriver>::make();
}
```

每个后端的 `.cpp` 末尾还有显式模板实例化，确保编译器生成完整代码：
```cpp
// 例如 OpenGLDriver.cpp:4979
template class ConcreteDispatcher<OpenGLDriver>;
```

### 完整初始化时序

```
FEngine::init()
  │
  │  ::new(&mDriverApiStorage) DriverApi(*mDriver, mCommandBufferQueue.getCircularBuffer());
  │                                       │
  └─► CommandStream::CommandStream(driver, buffer)
        │
        │  mDispatcher(driver.getDispatcher())
        │              │
        │              └─► OpenGLDriver::getDispatcher()        // 虚函数调用（仅此一次）
        │                    │
        │                    └─► ConcreteDispatcher<OpenGLDriver>::make()
        │                          │
        │                          │  // 宏展开，填充每一个函数指针：
        │                          │  dispatcher.beginFrame_    = &ConcreteDispatcher<OpenGLDriver>::beginFrame;
        │                          │  dispatcher.draw2_         = &ConcreteDispatcher<OpenGLDriver>::draw2;
        │                          │  dispatcher.createTexture_ = &ConcreteDispatcher<OpenGLDriver>::createTexture;
        │                          │  ...
        │                          └─► return dispatcher;
        │
        └─► mDispatcher = dispatcher;  // 值拷贝，之后不再需要虚函数调用
```

**关键点**：`getDispatcher()` 是整个 Dispatcher 机制中 **唯一一次虚函数调用**，发生在 CommandStream 构造时。之后 Dispatcher 以值的形式存在 CommandStream 中，所有命令编码/执行都通过函数指针直接分派，不再涉及虚函数。

## 完整数据流

```
初始化:
  ConcreteDispatcher<OpenGLDriver>::make()
    → 填充 Dispatcher 所有函数指针
    → Driver::getDispatcher() 返回
    → CommandStream 构造时拷贝一份

编码 (主线程):
  commandStream.draw2(offset, count, inst)
    → new Cmd(mDispatcher.draw2_, args)
                ↑ 函数指针存入 Command

执行 (渲染线程):
  command->execute(driver)
    → mExecute(driver, this, &next)
    → ConcreteDispatcher<OpenGLDriver>::draw2(driver, base, next)
        → static_cast<OpenGLDriver&>(driver)
        → Cmd::execute(&OpenGLDriver::draw2, concreteDriver, base, next)
            → apply → invoke → concreteDriver.draw2(args...)
                                → glDrawElements(...)
```

简而言之，Dispatcher 是一个 **手写的、编译时确定的虚函数表**，让命令流在不知道具体后端类型的情况下，以最低开销完成从 `CommandBase::mExecute` 到 `OpenGLDriver::draw2()` 的分派。

## 依赖关系

- `DriverAPI.inc` - 通过宏展开生成成员声明
- `CommandStreamDispatcher.h` - `ConcreteDispatcher<T>` 模板，生成具体 Driver 的函数指针表
- `CommandStream.h` - 持有 Dispatcher 副本，编码时取出函数指针
- `Driver.h` - `getDispatcher()` 返回由具体 Driver 子类创建的 Dispatcher
