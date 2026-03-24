# AsyncTaskCounter

## 文件概述

`AsyncTaskCounter` 是一个线程安全的异步任务计数器，用于追踪特定类型的进行中任务数量，并支持等待所有任务完成。

## 核心类/结构体

- **`webgpuutils::AsyncTaskCounter`**
  - `startTask()` - 原子递增任务计数
  - `finishTask()` - 原子递减任务计数，为零时通知所有等待者
  - `waitForAllToFinish()` - 阻塞直到计数为 0
  - `isIdle()` - 非阻塞检查计数是否为 0

## 关键实现逻辑

- 使用 `std::mutex` + `std::condition_variable` 实现线程安全
- `finishTask` 中包含断言确保计数不会变为负数
- 不可拷贝、不可移动（防止误用）

## 依赖关系

- `utils/debug.h` - `assert_invariant`
