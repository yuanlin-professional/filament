# VulkanMemory (VulkanMemory.h / VulkanMemory.cpp)

## 文件概述

Vulkan 内存管理的底层封装，集成 Vulkan Memory Allocator (VMA) 库。定义 GPU 缓冲区数据结构和缓冲区绑定类型枚举。

## 核心类/结构体

### `VulkanBufferBinding` 枚举
GPU 缓冲区的绑定用途类型：
- UNKNOWN / VERTEX / INDEX / UNIFORM / SHADER_STORAGE

### `VulkanGpuBuffer` 结构体
底层 GPU 缓冲区描述符：
- `vkbuffer` - VkBuffer 句柄
- `vmaAllocation` - VMA 内存分配句柄
- `allocationInfo` - VMA 分配信息（包含映射地址等）
- `numBytes` - 缓冲区大小
- `binding` - 缓冲区绑定类型

## 关键实现逻辑

- **VMA 配置**: 禁用静态 Vulkan 函数绑定（`VMA_STATIC_VULKAN_FUNCTIONS=0`），启用动态绑定（`VMA_DYNAMIC_VULKAN_FUNCTIONS=1`），与 BlueVK 函数加载器配合
- **头文件顺序**: `bluevk/BlueVK.h` 必须在 `vk_mem_alloc.h` 之前包含
- **不透明句柄**: VmaAllocator、VmaAllocation、VmaPool 通过 `VK_DEFINE_HANDLE` 前向声明

## 依赖关系

- `bluevk/BlueVK.h` - Vulkan 函数加载器（必须先包含）
- `vk_mem_alloc.h` - Vulkan Memory Allocator 库
