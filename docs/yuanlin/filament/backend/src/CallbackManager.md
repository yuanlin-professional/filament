# CallbackManager

## 文件概述

`CallbackManager.h` / `CallbackManager.cpp` 实现了一个基于条件计数的回调调度器。用于在所有先前注册的条件都被满足后，自动触发用户指定的回调函数。典型应用场景是 GPU 异步操作完成通知。

## 核心类/结构体

### `CallbackManager`
- 内部维护一个 `Callback` 链表（`std::list`），每个节点包含原子计数器、回调处理器和回调函数。
- **get()**: 创建一个新条件，增加当前回调节点的计数器，返回句柄 `Handle`。
- **put()**: 标记条件已满足（计数器减一）。当计数归零且回调已设置时，通过 `DriverBase::scheduleCallback` 调度回调。
- **setCallback()**: 设置新的回调函数。如果此前所有条件已满足（计数为零），回调立即被调度。原子性地创建新的条件插槽。
- **terminate()**: 关闭时调用所有挂起的回调，避免资源泄漏。

## 关键实现逻辑

- 采用生产者-消费者模式：`get()`/`put()` 通常在不同线程调用（如 GPU 线程和渲染线程）。
- 原子计数器 `count` 确保条件检查的线程安全性，无需在热路径上持锁。
- `allocateNewSlot()` 在链表末尾添加新节点，旧节点仍保持有效直到条件满足后被销毁。
- 使用 `std::list` 确保迭代器在插入/删除操作后仍然有效。

## 依赖关系

- `DriverBase.h` - `DriverBase::scheduleCallback` 回调调度
- `backend/CallbackHandler.h` - 回调处理器接口
- `utils::Mutex` - 互斥锁保护链表操作
