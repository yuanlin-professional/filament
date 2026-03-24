# UboManager

## 文件概述

`UboManager` 管理 MaterialInstance 的 UBO（Uniform Buffer Object）批处理分配。将多个 MaterialInstance 的 uniform 数据合并到一个共享 UBO 中，通过偏移量绑定减少 GPU 状态切换次数，提升渲染性能。该类非线程安全，设计为在单个核心线程上使用。

## 核心类/结构体

### `UboManager`
- 内部使用 `BufferAllocator` 管理共享 UBO 的内存分配
- `mPendingInstances` -- 等待分配 UBO 槽位的新 MaterialInstance
- `mManagedInstances` -- 已分配槽位的所有 MaterialInstance
- 缓冲区增长因子 `BUFFER_SIZE_GROWTH_MULTIPLIER = 1.5f`

### `UboManager::FenceManager`
- GPU 资源回收管理器，使用后端 fence 跟踪帧间 GPU 资源使用
- `track()` -- 在帧结束时关联 fence 和分配 ID 集合
- `reclaimCompletedResources()` -- 在帧开始时回收已完成帧的资源

## 关键实现逻辑

- **beginFrame()** -- 核心方法：(1) 回收 GPU 已完成的槽位 (2) 为新实例分配槽位 (3) 容量不足时扩容 (4) 映射共享 UBO 到 CPU 地址空间
- **finishBeginFrame()** -- 取消 UBO 映射，完成本帧的 uniform 数据写入
- **endFrame()** -- 创建 fence 追踪本帧使用的分配 ID，递增 GPU 使用计数
- **reallocate()** -- 当分配失败时，以 1.5x 增长因子重新分配更大的 UBO，重置所有分配器状态

## 依赖关系

- `details/BufferAllocator.h` -- 内存槽位分配器
- `backend/DriverApi.h` -- 驱动 API（创建 BufferObject、Fence、内存映射等）

## 与公共 API 的关系

不直接对应公共 API，作为 `FEngine` 的内部优化组件。通过 `Engine::Config` 中的 `sharedUboInitialSizeInBytes` 配置初始大小，需启用 `enable_material_instance_uniform_batching` 特性标志。
