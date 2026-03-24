# HandleAllocator

## 文件概述

`HandleAllocator.cpp` 实现了 Filament 后端的句柄内存分配器。使用三级内存池（按对象大小分档）提供高效的固定大小分配，并在池耗尽时回退到堆分配。是后端资源管理的核心组件。

## 核心类/结构体

### `HandleAllocator<P0, P1, P2>`
- 模板类，P0/P1/P2 为三个内存池的块大小。
- **Allocator::Allocator()**: 初始化三个内存池，将堆内存按比例分配给 Pool0/Pool1/Pool2。初始化时 `memset` 清零确保 age 从 0 开始。
- **handleToPointerSlow()**: 在溢出映射表中查找堆分配的句柄对应的指针。
- **allocateHandleSlow()**: 池分配失败时回退到 `malloc`，生成带 `HANDLE_HEAP_FLAG` 标记的句柄 ID。
- **deallocateHandleSlow()**: 释放堆分配的句柄，从溢出映射表中移除并 `free`。
- **getHandleTag()**: 调试模式下获取句柄的标签字符串。

### `DebugTag`
- 管理句柄的调试标签映射表（`mDebugTags`）。
- **writePoolHandleTag()** / **writeHeapHandleTag()**: 写入标签，pool 标签可回收，heap 标签永久保留。
- **findHandleTag()**: 根据键查找标签，未找到返回 "(no tag)"。
- 预分配 512 条标签空间以减少初期的频繁 `malloc`。

## 关键实现逻辑

- 三级分档策略：小对象 -> Pool0，中等对象 -> Pool1，大对象 -> Pool2。每个池包含等量的块数。
- 句柄 ID 编码：池分配的句柄 ID 基于内存偏移计算（除以对齐值），堆分配的句柄 ID 带有高位标志 `HANDLE_HEAP_FLAG`。
- 使用 `HANDLE_AGE_MASK` 实现 use-after-free 检测：每次释放后 age 递增，再次使用时与句柄中的 age 比较。
- 溢出映射表（`mOverflowMap`）使用互斥锁保护，仅在池耗尽的罕见情况下使用。
- 为 OpenGL、Vulkan、Metal、WebGPU 四种后端提供显式模板实例化。

## 依赖关系

- `backend/Handle.h` - `HandleBase::HandleId` 类型
- `utils::Allocator` - 底层池分配器
- `utils::CString` / `utils::ImmutableCString` - 标签字符串
- `utils::Panic` / `utils::Logger` - 错误报告和日志
