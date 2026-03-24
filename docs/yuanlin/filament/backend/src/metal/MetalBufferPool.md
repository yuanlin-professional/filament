# MetalBufferPool

## 文件概述

MetalBufferPool 实现了 Metal 后端的暂存缓冲区池化管理和线性 bump 分配器。缓冲区池用于纹理上传和缓冲区拷贝时的临时暂存缓冲区复用，减少频繁创建/销毁 Metal 缓冲区的开销。Bump 分配器则提供了更高效的线性分配策略，适用于顶点/索引缓冲区上传。

**源文件**: `MetalBufferPool.h`, `MetalBufferPool.mm`

## 核心类/结构体

- **`MetalBufferPoolEntry`** - 不可变 POD，表示一个共享的 CPU-GPU 缓冲区条目，包含 `TrackedMetalBuffer`、容量、最后访问时间和引用计数
- **`MetalBufferPool`** - 缓冲区池管理器
  - `acquireBuffer(size_t)` - 获取至少指定大小的缓冲区（优先复用空闲池中的）
  - `retainBuffer/releaseBuffer` - 引用计数管理，引用归零时回收到空闲池
  - `gc()` - 垃圾回收，驱逐超过 10 帧未使用的空闲缓冲区
  - `reset()` - 销毁所有空闲缓冲区
- **`MetalBumpAllocator`** - 线性 bump 分配器
  - 在固定容量的缓冲区上递增分配，4 字节对齐
  - 容量不足时创建新缓冲区继续分配
  - 超过单缓冲区容量的请求直接创建独立缓冲区

## 关键实现逻辑

- **池化复用**: 空闲缓冲区使用 `std::multimap<size_t, entry*>` 按容量有序存储，通过 `lower_bound` 快速找到满足需求的最小缓冲区
- **线程安全**: 所有 acquireBuffer/releaseBuffer/gc/reset 操作都通过 `std::mutex` 保护，因为 acquire 和 release 可能在不同线程调用（引擎线程 vs Metal 回调线程）
- **LRU 驱逐**: `gc()` 每帧递增帧计数器（原子变量），驱逐 `lastAccessed < currentFrame - 10` 的空闲缓冲区
- **Bump 分配**: 所有暂存缓冲区使用 `MTLStorageModeShared`，分配头指针 4 字节对齐后递增

## 依赖关系

- `MetalBuffer.h` - TrackedMetalBuffer 类型
- `MetalContext.h` - 访问 Metal 设备
- `Metal/Metal.h` - MTLBuffer、MTLDevice
