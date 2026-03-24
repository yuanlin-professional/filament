# CommandStream

## 文件概述

`CommandStream.cpp` 实现了命令流的执行引擎和调试支持。命令流是 Filament 后端架构的核心，负责将应用线程发出的 Driver API 调用序列化为命令对象，然后在渲染线程上反序列化并执行。

## 核心类/结构体

### `CommandStream`
- 头文件位于 `private/backend/CommandStream.h`。
- **构造函数**: 接受 `Driver` 引用和 `CircularBuffer` 引用，获取 Dispatcher 分发表。在 Android 上可通过系统属性启用性能计数器。
- **execute()**: 核心执行方法。接受缓冲区指针，遍历命令链表依次执行每个命令的 `execute` 方法，直到遇到空指针（终止标记）。
- **queueCommand()**: 将自定义 `std::function` 命令排入命令流。

### `CustomCommand`
- 封装 `std::function<void()>` 的自定义命令类型。
- **execute()**: 调用存储的函数对象后析构自身。

## 关键实现逻辑

- 命令执行通过 `Driver::execute()` 包装，允许具体驱动（如 OpenGL）在特定上下文中执行命令。
- 命令链表结构：每个 `CommandBase` 的 `execute` 方法返回下一个命令的指针，NULL 表示结束。
- 性能计数器（可选）：使用 `Profiler` 测量 CPU 周期、分支预测失误等硬件计数器，结果通过 systrace 输出。
- `DEBUG_COMMAND_STREAM` 模式下，每个命令执行时输出反混淆的方法名和参数，通过 `DriverAPI.inc` 宏展开实现。

## 依赖关系

- `private/backend/CommandStream.h` - 命令基类和类型定义
- `private/backend/Driver.h` - Driver 接口
- `utils::Profiler` - 硬件性能计数器
- `utils::CallStack` - 类型名反混淆（仅调试）
- `private/utils/Tracing.h` - systrace 追踪
