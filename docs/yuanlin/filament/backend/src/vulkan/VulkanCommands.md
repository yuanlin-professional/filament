# VulkanCommands (VulkanCommands.h / VulkanCommands.cpp)

## 文件概述

管理 Vulkan 命令缓冲区的提交、同步和生命周期。提供命令缓冲区池，支持多个命令缓冲区的并发录制和顺序提交，通过 Fence 和信号量实现 CPU-GPU 及 GPU-GPU 同步。

## 核心类/结构体

### `VulkanCommandBuffer` 结构体
单个命令缓冲区的封装：
- `acquire(resource)` - 将资源引用绑定到命令缓冲区的生命周期
- `reset()` - 重置命令缓冲区状态
- 持有 VkCommandBuffer、VulkanCmdFence（提交围栏）和资源引用列表

### `VulkanCommands` 类
命令缓冲区管理器，维护 `FVK_MAX_COMMAND_BUFFERS` 个命令缓冲区的循环池：
- `get()` - 获取当前可用的命令缓冲区
- `flush()` - 提交当前命令缓冲区到 GPU
- `wait()` / `gc()` - 等待完成和垃圾回收

### `VulkanGroupMarkers` 类（调试模式）
管理调试分组标记的栈结构，支持 push/pop 操作和时间戳记录。

## 关键实现逻辑

- **命令缓冲区池**: 使用固定大小数组循环复用命令缓冲区，通过 Fence 判断是否可复用
- **同步机制**: 每个命令缓冲区关联一个 VulkanCmdFence，提交时设状态为 NOT_READY，完成时由 GPU 信号设为 SUCCESS
- **资源生命周期绑定**: `acquire()` 确保资源在命令缓冲区执行期间不被销毁
- **信号量管理**: 通过 VulkanSemaphoreManager 管理 GPU-GPU 同步信号量

## 依赖关系

- `VulkanAsyncHandles.h` - VulkanCmdFence 定义
- `VulkanConstants.h` - FVK_MAX_COMMAND_BUFFERS
- `VulkanContext.h` - Vulkan 上下文
- `VulkanSemaphoreManager.h` - 信号量管理
- `vulkan/memory/ResourcePointer.h` - 资源引用
- `vulkan/utils/StaticVector.h` - 资源列表容器
