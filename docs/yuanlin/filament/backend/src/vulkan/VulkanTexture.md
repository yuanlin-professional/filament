# VulkanTexture (VulkanTexture.h / VulkanTexture.cpp)

## 文件概述

Vulkan 纹理管理，包括纹理状态、图像视图创建、布局追踪和数据上传。同时定义了 VulkanStream（流式图像源）和 VulkanTextureState（纹理共享状态）。

## 核心类/结构体

### `VulkanStream`
流式图像源，管理通过 `acquire()` 获取的外部图像帧。前端线程和后端线程分别访问不同的数据成员。维护 AHB 到 VulkanTexture 的映射缓存。

### `VulkanTextureState`
纹理的共享可变状态，继承自 Resource，持有：
- VmaAllocator 和 VmaAllocation（内存分配）
- VkImage 和 VkDeviceMemory
- VkImageView 缓存（基于子资源范围的哈希表）
- 每 mip 级别/层的布局追踪（RangeMap）
- VkSamplerYcbcrConversion（外部采样器转换）

### `VulkanTexture`
纹理主体结构，继承 HwTexture 和 Resource：
- 管理图像视图的创建和缓存
- 追踪每个子资源的当前 VulkanLayout
- 提供布局转换方法
- 支持 sidecar 纹理（用于 MSAA 解析等场景）

## 关键实现逻辑

- **布局追踪**: 使用 RangeMap 高效追踪任意 mip/layer 子资源范围的当前布局状态
- **图像视图缓存**: 基于 (VkImageSubresourceRange, VkComponentMapping, VkImageViewType) 组合键缓存 VkImageView
- **数据上传**: 通过 VulkanStagePool 的暂存缓冲区进行 CPU -> GPU 数据传输
- **外部图像**: 支持通过 VkSamplerYcbcrConversion 采样外部格式（如 Android AHB）

## 依赖关系

- `DriverBase.h` - HwTexture 基类
- `VulkanCommands.h` - 命令缓冲区
- `VulkanMemory.h` - VMA 内存分配
- `VulkanStagePool.h` - 暂存缓冲区
- `vulkan/memory/Resource.h` / `ResourcePointer.h` - 引用计数
- `vulkan/utils/Image.h` - VulkanLayout 和布局转换
