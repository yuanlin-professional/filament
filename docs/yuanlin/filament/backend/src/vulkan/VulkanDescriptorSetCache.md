# VulkanDescriptorSetCache (VulkanDescriptorSetCache.h / VulkanDescriptorSetCache.cpp)

## 文件概述

Vulkan 描述符集的分配和管理缓存。封装了描述符池（VkDescriptorPool）的创建和管理，提供描述符集的分配、更新和绑定功能。

## 核心类/结构体

### `VulkanDescriptorSetCache` 类
描述符集管理器（最多 4 个描述符集）：
- `updateBuffer(set, binding, bufferObject, offset, size)` - 更新缓冲区绑定
- `updateSampler(set, binding, texture, sampler, layout)` - 更新采样器绑定
- `terminate()` - 清理所有描述符池

### 类型别名
- `DescriptorSetLayoutArray` - 4 个 VkDescriptorSetLayout 的数组
- `DescriptorSetArray` - 4 个 resource_ptr<VulkanDescriptorSet> 的数组
- `DescriptorCount` - 各类型描述符的数量统计

## 关键实现逻辑

- **描述符池管理**: 当现有池耗尽时自动创建新的 VkDescriptorPool
- **描述符集分配**: 从池中分配 VkDescriptorSet 并包装为 VulkanDescriptorSet resource_ptr
- **更新操作**: 使用 vkUpdateDescriptorSets 批量更新描述符绑定
- **外部采样器**: 支持需要特殊 VkDescriptorSetLayout 的外部采样器纹理

## 依赖关系

- `VulkanHandles.h` - VulkanDescriptorSet/VulkanDescriptorSetLayout 定义
- `vulkan/memory/ResourcePointer.h` - resource_ptr 引用
- `vulkan/utils/Definitions.h` - DescriptorSetMask 等类型
- `bluevk/BlueVK.h` - Vulkan 描述符类型
- `tsl/robin_map.h` - 哈希表
