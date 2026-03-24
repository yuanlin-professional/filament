# BufferAllocator

## 文件概述

`BufferAllocator` 实现了一个基于槽位（Slot）的内存分配器，用于在共享 UBO（Uniform Buffer Object）中为多个 MaterialInstance 分配和管理内存区域。它采用伙伴系统（Buddy System）思想，支持分配、释放、合并空闲块等操作。该类非线程安全，需外部同步。

## 核心类/结构体

### `BufferAllocator`
- **Slot** -- 描述一个内存槽位：偏移量、大小、是否已分配、GPU 使用计数
- **InternalSlotNode** -- 内部节点，包含 Slot 和空闲列表迭代器
- **AllocationId** -- 基于偏移量的 1-based ID（0 表示未分配，~0u 表示需重新分配）

## 关键实现逻辑

- **allocate()** -- 使用 Best-Fit 策略从 `mFreeList`（`std::multimap`）中查找最小适配块，按需拆分剩余空间
- **freeSlot()** -- 释放后检查左右邻居，执行相邻空闲块合并（类似伙伴系统合并）
- **头尾标记** -- 每个分配块在首尾均存储节点副本，便于通过尾部快速定位前一个块的头部，实现 O(1) 邻居合并
- **GPU 引用计数** -- `acquireGpu()`/`releaseGpu()` 管理 GPU 读取锁，防止 GPU 仍在使用的槽位被过早回收
- **alignUp()** -- 将请求大小向上对齐到 `mSlotSize`（源自 GPU UBO 对齐要求，通常为 256 字节）

## 依赖关系

- `utils/FixedCapacityVector.h` -- 固定容量向量，存储节点数组
- `utils/Panic.h`、`utils/debug.h` -- 断言和调试工具

## 与公共 API 的关系

不直接对应公共 API，作为 `UboManager` 的内部分配器使用，支撑 MaterialInstance 的 UBO 批处理（Uniform Batching）功能。
