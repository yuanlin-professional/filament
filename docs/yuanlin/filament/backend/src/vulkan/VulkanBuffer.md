# VulkanBuffer (VulkanBuffer.h)

## 文件概述

定义 VulkanBuffer 类，作为 GPU 缓冲区（VulkanGpuBuffer）的引用计数包装器。通过回调函数实现缓冲区的自动回收，与 VulkanBufferCache 配合实现缓冲区池化。

## 核心类/结构体

### `VulkanBuffer` 类
继承自 `fvkmemory::Resource`，持有对 VulkanGpuBuffer 的非拥有引用：
- `VulkanBuffer(gpuBuffer, onRecycleFn)` - 构造时绑定 GPU 缓冲区和回收回调
- `~VulkanBuffer()` - 析构时调用回收回调将缓冲区归还池
- `getGpuBuffer()` - 获取底层 GPU 缓冲区指针

### `OnRecycle` 回调类型
`std::function<void(VulkanGpuBuffer const*)>`，供 VulkanBufferCache 注册回收逻辑。

## 关键实现逻辑

- **池化回收**: VulkanBuffer 析构时不销毁底层 VkBuffer，而是通过回调将 VulkanGpuBuffer 归还缓冲区池供后续复用
- **引用计数**: 继承 Resource 基类，当所有 resource_ptr 引用释放后自动触发回收

## 依赖关系

- `VulkanMemory.h` - VulkanGpuBuffer 定义
- `memory/Resource.h` - fvkmemory::Resource 基类
