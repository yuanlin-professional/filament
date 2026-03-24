# VulkanBufferCache (VulkanBufferCache.h / VulkanBufferCache.cpp)

## 文件概述

GPU 缓冲区池管理器，负责 VkBuffer 的分配、复用和回收。按缓冲区绑定类型（顶点/索引/Uniform/SSBO）和大小维护多个缓冲区池，减少频繁的 VkBuffer 创建/销毁开销。

## 核心类/结构体

### `VulkanBufferCache` 类
缓冲区池管理器：
- `acquire(binding, numBytes)` - 从池中获取或新建一个满足大小要求的 VulkanBuffer
- `gc()` - 淘汰长时间未使用的缓冲区，递增帧号
- `terminate()` - 销毁所有池中的缓冲区

### `UnusedGpuBuffer` 结构体
记录未使用缓冲区的最后访问帧号，用于 gc 淘汰判断。

### `BufferPool` 类型
`std::multimap<uint32_t, UnusedGpuBuffer>`，按字节大小排序，支持快速查找满足大小要求的最小缓冲区。

## 关键实现逻辑

- **按类型分池**: 为不同绑定类型维护独立的缓冲区池
- **UMA 优化**: 在统一内存架构上，Uniform 缓冲区同时设为 HOST_VISIBLE，避免暂存拷贝
- **回收机制**: VulkanBuffer 析构时通过 OnRecycle 回调将底层 VulkanGpuBuffer 归还池

## 依赖关系

- `VulkanBuffer.h` - VulkanBuffer 定义
- `VulkanContext.h` - UMA 检测
- `VulkanMemory.h` - VMA 分配器和 VulkanGpuBuffer
- `memory/Resource.h` / `memory/ResourceManager.h` - 资源管理
