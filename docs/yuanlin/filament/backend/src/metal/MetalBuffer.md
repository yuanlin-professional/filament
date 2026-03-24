# MetalBuffer

## 文件概述

MetalBuffer 实现了 Metal 后端的 GPU 缓冲区管理，包括缓冲区的创建、数据上传、绑定到渲染管线等功能。同时提供了缓冲区生命周期跟踪（`TrackedMetalBuffer`）、分配耗时监控（`ScopedAllocationTimer`）和环形缓冲区（`MetalRingBuffer`）等辅助功能。

**源文件**: `MetalBuffer.h`, `MetalBuffer.mm`

## 核心类/结构体

- **`ScopedAllocationTimer`** - RAII 计时器，监控缓冲区分配耗时，超过 10 秒时通过 `PlatformMetal::debugUpdateStat` 上报
- **`TrackedMetalBuffer`** - 对 `id<MTLBuffer>` 的包装，跟踪存活缓冲区数量（按类型：GENERIC、RING、STAGING、DESCRIPTOR_SET），超过 30000 个时上报警告
- **`MetalBuffer`** - 核心缓冲区类，管理 GPU 私有缓冲区的创建和数据上传
  - 支持两种上传策略：`POOL`（使用 MetalBufferPool 的暂存缓冲区）和 `BUMP_ALLOCATOR`（使用 MetalBumpAllocator）
  - `Stage` 枚举定义绑定阶段：VERTEX、FRAGMENT、COMPUTE
- **`MetalRingBuffer`** - 环形缓冲区，用于 argument buffer 等需要频繁更新的场景。通过槽位轮转复用内存，满时回退到单独分配辅助缓冲区

## 关键实现逻辑

- **上传策略选择**: 顶点/索引缓冲区使用 `BUMP_ALLOCATOR`（当容量 > 0 时），其他使用 `POOL`
- **存储模式**: 默认 `MTLResourceStorageModePrivate`；当设置 `SHARED_WRITE_BIT` 时，iOS/Apple Silicon 使用 `Shared`，Intel Mac 使用 `Managed`
- **数据上传**: 通过 `MTLBlitCommandEncoder` 从暂存缓冲区拷贝到 GPU 私有缓冲区，偏移量必须 4 字节对齐
- **批量绑定**: `bindBuffers` 静态方法将多个缓冲区一次性绑定到渲染/计算编码器的指定阶段
- **环形缓冲区槽位管理**: 使用 `shared_ptr<atomic>` 跟踪已占用槽位数，通过 command buffer 完成回调释放

## 依赖关系

- `MetalContext.h` - 访问设备、缓冲区池、bump allocator
- `MetalFlags.h` - 调试标签宏定义
- `MetalUtils.h` - uniform buffer 偏移对齐
- `MetalBufferPool.h` - 暂存缓冲区池
- `backend/DriverEnums.h` - BufferObjectBinding、BufferUsage
- `backend/platforms/PlatformMetal.h` - 调试统计上报
