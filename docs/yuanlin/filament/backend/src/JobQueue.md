# JobQueue

## 文件概述

`JobQueue.h` / `JobQueue.cpp` 实现了一个线程安全的生产者-消费者任务队列系统，支持任务取消、批量处理和预发行任务 ID。提供两种工作者模式：分摊工作者（AmortizationWorker，非线程）和线程工作者（ThreadWorker）。

## 核心类/结构体

### `JobQueue`
- **push()**: 将任务入队，支持使用预发行 ID。队列关闭后返回 `InvalidJobId`。
- **pop()**: 取出下一个任务。`shouldBlock=true` 时阻塞等待（线程工作者用）；`false` 时非阻塞返回（分摊工作者用）。
- **popBatch()**: 批量取出任务，非阻塞。`maxJobsToPop < 0` 时取出所有任务。
- **issueJobId()**: 预分配任务 ID，用于先注册 ID 后填充任务内容的场景。
- **cancel()**: 根据 ID 取消已入队但未执行的任务。
- **stop()**: 关闭队列，阻止新任务入队，唤醒所有等待线程。

### `JobWorker`（抽象基类）
- **process()**: 处理一批任务（仅非线程工作者使用）。
- **terminate()**: 终止工作者，调用 `queue->stop()`。

### `AmortizationWorker`
- 非线程工作者，通过调用者显式调用 `process()` 驱动。
- 适合在主循环中每帧处理固定数量任务的场景。
- `terminate()` 时排空所有剩余任务。

### `ThreadWorker`
- 独立线程工作者，创建时启动后台线程。
- 支持配置线程名称、优先级、启动/结束回调。
- 通过阻塞式 `pop(true)` 持续处理任务直到队列关闭。

## 关键实现逻辑

- 任务存储使用 `unordered_map<JobId, Job>` + `queue<JobId>` 分离映射和顺序。取消任务只需从 map 中移除，pop 时自动跳过已取消的 ID。
- 预发行 ID 机制：`issueJobId()` 在 map 中预占位（空 Job），`push()` 时检查占位是否存在。
- 批量操作 `popBatch()` 使用 `FixedCapacityVector` 避免动态分配。
- 所有公共方法均通过 `mutex` + `condition_variable` 保证线程安全。

## 依赖关系

- `utils::Invocable` - 类型擦除的可调用对象（Job 类型）
- `utils::FixedCapacityVector` - 固定容量向量（批量返回）
- `utils::JobSystem` - 线程命名和优先级设置
