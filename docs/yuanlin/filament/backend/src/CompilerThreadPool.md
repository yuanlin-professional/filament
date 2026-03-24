# CompilerThreadPool

## 文件概述

`CompilerThreadPool.h` / `CompilerThreadPool.cpp` 实现了 GPU 着色器编译的线程池。支持多优先级队列，允许关键着色器优先编译，同时避免阻塞主渲染线程。

## 核心类/结构体

### `ProgramToken`
- 虚基类，用作编译任务的标识令牌。通过 `shared_ptr` 管理生命周期（`program_token_t`）。

### `CompilerThreadPool`
- **init()**: 启动指定数量的编译线程，每个线程执行 `threadSetup` 回调后进入工作循环。
- **terminate()**: 设置退出标志，唤醒所有线程，等待其结束，清空所有队列。
- **queue()**: 将编译任务按优先级入队（`CompilerPriorityQueue` 有 3 个级别）。
- **dequeue()**: 根据 `program_token_t` 查找并取消排队中的任务（用于主线程急需该着色器时提前执行）。
- **find()**: 在所有队列中查找指定 token 的任务。

## 关键实现逻辑

- 3 个优先级队列（`mQueues[3]`），工作线程总是从最高优先级的非空队列中取任务。
- 每个队列是 `std::deque<pair<program_token_t, Job>>`，支持 FIFO 顺序。
- `dequeue()` 允许主线程"抢回"尚未被工作线程取走的编译任务，在主线程上同步执行以减少等待时间。
- 线程安全通过 `Mutex` + `Condition` 实现，工作线程在队列为空时阻塞。
- `ThreadSetup` 和 `ThreadCleanup` 回调用于 GPU 上下文初始化/清理（如 EGL context 绑定）。

## 依赖关系

- `backend/DriverEnums.h` - `CompilerPriorityQueue` 枚举
- `utils::Invocable` - 类型擦除的可调用对象
- `utils::Mutex` / `utils::Condition` - 同步原语
- `private/utils/Tracing.h` - 性能追踪
