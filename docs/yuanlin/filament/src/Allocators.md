# Allocators.h

## 文件概述

定义 Filament 引擎内部使用的内存分配器类型别名。根据构建模式（Debug/Release）选择不同的分配策略，在 Debug 模式下启用追踪和互斥锁，在 Release 模式下使用无锁无追踪方案以获得最佳性能。

## 核心类/结构体/函数

### 类型别名

- **`HeapAllocatorArena`** -- 基于堆分配的 Arena。Debug 模式使用 `Mutex` 锁和 `DebugAndHighWatermark` 追踪策略；Release 模式使用 `NoLock` 和 `Untracked`，因为 `malloc/free` 本身是线程安全的。
- **`LinearAllocatorArena`** -- 基于线性分配器的 Arena。Debug 模式启用追踪策略，Release 模式无追踪。线性分配器只能顺序分配，适合帧级生命周期的临时内存。
- **`RootArenaScope`** -- `LinearAllocatorArena` 的 ArenaScope 别名，用于管理线性分配器的作用域。

## 关键实现逻辑

采用条件编译（`#ifndef NDEBUG`）在 Debug 和 Release 构建之间切换分配策略。Debug 模式下 `HeapAllocatorArena` 需要互斥锁来同步追踪策略；Release 模式下堆分配器不需要额外锁，追踪也被禁用以最大化性能。

## 依赖关系

- `utils/Allocator.h` -- 提供 Arena、HeapAllocator、LinearAllocator 等基础设施
- `private/backend/BackendUtils.h` -- 后端工具

## 被引用关系

被 `Froxelizer.h` 等需要帧内临时内存分配的模块大量使用。`RootArenaScope` 在渲染管线中用于每帧的临时数据分配。
