# VulkanDescriptorSetLayoutCache (VulkanDescriptorSetLayoutCache.h / VulkanDescriptorSetLayoutCache.cpp)

## 文件概述

VkDescriptorSetLayout 的缓存管理器。相同绑定配置的描述符集布局可以复用，本类通过位掩码哈希实现布局的去重和缓存。

## 核心类/结构体

### `VulkanDescriptorSetLayoutCache` 类
描述符集布局缓存：
- `createLayout(handle, info)` - 根据 Filament DescriptorSetLayout 描述创建或复用布局
- `getVkLayout(bitmasks, externalSamplers, immutableSamplers)` - 直接从位掩码获取 VkDescriptorSetLayout
- `terminate()` - 销毁所有缓存的布局

### `LayoutKey` 结构体
布局缓存键值：
- `bitmask` - 描述符集的位掩码表示（UBO/采样器/输入附件等）
- `immutableSamplerHash` - 不可变采样器的哈希值

## 关键实现逻辑

- **位掩码编码**: 将描述符布局编码为紧凑的位掩码（40 字节 Bitmask），用于高效比较和哈希
- **不可变采样器支持**: 外部采样器需要绑定到布局中的不可变采样器，通过额外哈希区分
- **robin_map 缓存**: 使用 LayoutKey 哈希进行 O(1) 查找

## 依赖关系

- `VulkanHandles.h` - VulkanDescriptorSetLayout 定义
- `vulkan/memory/ResourcePointer.h` - resource_ptr
- `bluevk/BlueVK.h` - VkDescriptorSetLayout 类型
- `tsl/robin_map.h` - 哈希表
