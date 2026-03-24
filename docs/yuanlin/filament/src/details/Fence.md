# Fence

## 文件概述

`FFence` 是 `Fence` 公共 API 的私有实现类，提供 CPU-GPU 同步机制。通过在命令流中插入信号点，允许 CPU 端等待 GPU 端完成特定操作。使用共享的静态互斥锁和条件变量实现低开销的多 fence 管理。

## 核心类/结构体

### `FFence`
- 继承自 `Fence`
- 持有 `std::shared_ptr<FenceSignal>` 实现信号管理
- 共享静态 `sLock`/`sCondition` 避免每个 fence 独立分配同步原语

### `FFence::FenceSignal`
- 内部信号状态机：`UNSIGNALED` -> `SIGNALED` 或 `DESTROYED`
- `signal()` -- 在驱动线程设置状态并唤醒等待线程
- `wait()` -- 在主线程阻塞等待状态变化

## 关键实现逻辑

- **构造** -- 创建时通过 `driverApi.queueCommand()` 向命令流插入一个信号回调
- **wait()** -- 支持 FLUSH 模式（先刷新命令缓冲区再等待）和 DONT_FLUSH 模式
- **平台事件泵送** -- 某些平台需要在等待期间持续处理平台事件以避免死锁，采用 1ms 轮询间隔
- **waitAndDestroy()** -- 便利方法，等待完成后自动销毁 fence

## 依赖关系

- `details/Engine.h` -- 引擎实例，用于刷新命令和平台事件泵送
- `utils/Condition.h`、`utils/Mutex.h` -- 同步原语

## 与公共 API 的关系

直接对应 `filament::Fence`。通过 `Engine::createFence()` 创建，用于 `Engine::flushAndWait()` 等同步操作。
