# VulkanBufferProxy (VulkanBufferProxy.h / VulkanBufferProxy.cpp)

## 文件概述

GPU 缓冲区的动态代理包装器。允许在运行时更换底层 VulkanBuffer 而不影响外部引用，支持从 CPU 数据加载、暂存缓冲区绕过优化等功能。

## 核心类/结构体

### `VulkanBufferProxy` 类
缓冲区代理，封装缓冲区的更新和访问逻辑：
- `loadFromCpu(commands, cpuData, offset, numBytes)` - 从 CPU 数据加载到 GPU 缓冲区
- `getVkBuffer()` - 获取底层 VkBuffer 句柄
- `referencedBy(commands)` - 将当前缓冲区绑定到命令缓冲区生命周期

## 关键实现逻辑

- **暂存缓冲区绕过**: 当启用 `mStagingBufferBypassEnabled` 且缓冲区为 HOST_VISIBLE 时，直接通过内存映射写入，跳过暂存缓冲区拷贝
- **缓冲区替换**: 当缓冲区大小不足时，从 VulkanBufferCache 获取新缓冲区替换旧的
- **读取年龄追踪**: `mLastReadAge` 追踪缓冲区最后被读取的时间，避免写入正在被 GPU 使用的缓冲区

## 依赖关系

- `VulkanBufferCache.h` - 缓冲区池
- `VulkanCommands.h` - 命令缓冲区
- `VulkanContext.h` - 上下文信息
- `VulkanMemory.h` - GPU 缓冲区类型
- `VulkanStagePool.h` - 暂存缓冲区池
