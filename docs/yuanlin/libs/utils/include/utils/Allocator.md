# Allocator.h / Allocator.cpp

## 文件概述
内存分配器框架，提供多种分配策略，是 Filament 引擎内存管理的核心基础设施。

## 核心类/结构体/函数
- **pointermath**: 指针算术工具命名空间，提供 `add`、`align` 等对齐辅助函数
- **LinearAllocator**: 线性分配器，顺序分配内存块，不支持单独释放，可回退到指定位置
- **HeapAllocator**: 堆分配器，使用 `malloc/free` 的简单封装
- **LinearAllocatorWithFallback**: 线性分配器 + 堆回退，线性区域耗尽后自动使用堆分配
- **FreeList**: 空闲链表，在固定内存区域中管理等大小元素的分配/回收
- **AtomicFreeList**: 无锁空闲链表，使用 CAS 原子操作实现线程安全的 pop/push
- **PoolAllocator**: 固定大小对象池，基于 FreeList 实现
- **PoolAllocatorWithFallback**: 带堆回退的对象池
- **Arena**: 通用竞技场分配器模板，组合分配策略、锁策略、跟踪策略和区域策略
- **ArenaScope**: RAII 作用域管理器，离开作用域时自动 rewind 并调用析构链
- **STLAllocator**: 适配 Arena 的 STL 兼容分配器

## 关键实现逻辑
- LinearAllocator 使用无分支分配：对齐后检查边界，成功则前移指针
- AtomicFreeList 使用带 tag 的 HeadPtr (8字节) 解决 ABA 问题
- Arena 模板通过策略模式组合锁(NoLock/Mutex)、跟踪(Untracked/HighWatermark/Debug)和区域(Heap/Static)
- TrackingPolicy::Debug 在分配时填充 0xeb、释放时填充 0xef，帮助检测未初始化访问和释放后使用

## 依赖关系
- `utils/compiler.h`, `utils/memalign.h`, `utils/Mutex.h`
- `utils/debug.h`, `utils/Log.h` (实现文件)
