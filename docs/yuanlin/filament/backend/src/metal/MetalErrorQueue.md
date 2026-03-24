# MetalErrorQueue

## 文件概述

MetalErrorQueue 是一个线程安全的错误队列，用于收集 Metal 命令缓冲区执行过程中产生的 `NSError`。命令缓冲区的完成回调在 GPU 线程触发，而错误处理在驱动线程进行，因此需要线程安全的队列来传递错误信息。

**源文件**: `MetalErrorQueue.h`（仅头文件）

## 核心类/结构体

- **`MetalErrorQueue`** - 线程安全的错误队列
  - `isEmpty()` - 无锁快速检查是否有错误（使用 `atomic<bool>`）
  - `push(NSError*)` - 将错误压入队列（加锁）
  - `flush(callback)` - 取出所有错误并逐一回调处理（加锁），处理后清空

## 关键实现逻辑

- **双层检查优化**: `isEmpty()` 使用 `atomic<bool>` 的 relaxed 读避免每次 flush 都加锁。只有当 `mHasErrors` 为 true 时才进入加锁路径
- **批量处理**: `flush()` 先通过 `std::swap` 将错误列表原子性地移出，释放锁后再逐一回调，最小化持锁时间
- **线程安全**: `push()` 在 Metal 命令缓冲区完成回调（GPU 线程）中调用，`flush()` 在驱动线程中调用

## 依赖关系

- `Foundation/Foundation.h` - NSError 类型
- `utils/compiler.h` - UTILS_LIKELY 宏
