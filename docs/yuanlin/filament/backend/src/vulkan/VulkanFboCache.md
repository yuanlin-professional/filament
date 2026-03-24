# VulkanFboCache (VulkanFboCache.h / VulkanFboCache.cpp)

## 文件概述

VkFramebuffer 和 VkRenderPass 的缓存管理器。VkFramebuffer 是渲染通道和图像视图之间的绑定关系，VkRenderPass 描述渲染通道的附件配置。两者都不消耗 GPU 内存，但创建有一定开销。

## 核心类/结构体

### `VulkanFboCache` 类
帧缓冲缓存管理器，维护两个缓存：
- 渲染通道缓存（RenderPassKey -> VulkanRenderPass）
- 帧缓冲缓存（FboKey -> VulkanFramebuffer）

### `RenderPassKey` 结构体
渲染通道的不可变状态键值：
- 颜色附件格式数组、深度格式
- 清除/丢弃标志（开始和结束时）
- 初始深度布局、采样数、解析掩码、延迟分配标志
- 子通道数量和视图掩码（multiview）

### `FboKey` 结构体
帧缓冲的键值：
- VkRenderPass 引用、尺寸、层数
- 颜色/解析/深度附件的 VkImageView 数组

## 关键实现逻辑

- **基于哈希的缓存**: 使用 robin_map 以 MurmurHash 为键进行 O(1) 查找
- **附件布局约定**: 最终颜色和解析附件使用 COLOR_ATTACHMENT 布局，深度使用 DEPTH_ATTACHMENT
- **可配置淘汰**: 帧缓冲支持可配置的淘汰时间（FBO eviction time）
- **资源句柄化**: VulkanFramebuffer 和 VulkanRenderPass 均为 Resource 子类，通过 resource_ptr 管理

## 依赖关系

- `VulkanContext.h` - VulkanLayout 等类型
- `vulkan/memory/Resource.h` / `ResourceManager.h` / `ResourcePointer.h` - 资源管理
- `backend/TargetBufferInfo.h` - MRT 常量
- `tsl/robin_map.h` - 高性能哈希表
