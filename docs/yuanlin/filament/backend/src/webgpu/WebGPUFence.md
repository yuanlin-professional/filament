# WebGPUFence

## 文件概述

`WebGPUFence` 是 `HwFence` 的 WebGPU 实现，用于 CPU-GPU 同步。它基于 `WebGPUSubmissionState` 共享指针实现等待和状态查询。

## 核心类/结构体

- **`WebGPUFence`** - GPU 围栏
  - `mState` - 共享的提交状态指针
  - `mLock` / `mCond` - 互斥锁和条件变量，用于等待状态初始化
  - `wait(timeout)` - 带超时的等待
  - `getStatus()` - 非阻塞状态查询（等效 `wait(0)`）

## 关键实现逻辑

1. **两阶段等待**: 先等待 `mState` 被设置（提交完成通知），再调用 `mState->waitForCompletion()` 等待 GPU 完成
2. **超时处理**: 使用 `steady_clock` 精确计算剩余超时，处理溢出和边界条件
3. **线程安全**: 通过 `condition_variable` 在 `setSubmissionState` 和 `wait` 间同步

## 依赖关系

- `WebGPUQueueManager.h` - `WebGPUSubmissionState` 定义
- `backend/DriverEnums.h` - `FenceStatus` 枚举
