# VulkanPipelineLayoutCache (VulkanPipelineLayoutCache.h / VulkanPipelineLayoutCache.cpp)

## 文件概述

VkPipelineLayout 的缓存管理器。管线布局由描述符集布局和推送常量范围组成，相同配置可复用。

## 核心类/结构体

### `VulkanPipelineLayoutCache` 类
管线布局缓存：
- `getLayout(descriptorSetLayouts, program)` - 根据描述符集布局和着色器程序获取或创建 VkPipelineLayout
- `terminate()` - 销毁所有缓存的管线布局

### `PushConstantKey` 结构体
推送常量配置：stage（着色器阶段）和 size（字节大小），偏移固定为 0。

### `PipelineLayoutKey` 结构体（40 字节）
管线布局缓存键值，包含 4 个 VkDescriptorSetLayout 和每个着色器阶段的推送常量配置。

## 关键实现逻辑

- **组合键**: 管线布局取决于描述符集布局和推送常量范围的组合
- **程序解析**: 从 VulkanProgram 中提取每个着色器阶段的推送常量大小
- **unordered_map 缓存**: 使用 MurmurHash 对 40 字节键值哈希

## 依赖关系

- `VulkanHandles.h` - VulkanProgram/VulkanDescriptorSetLayout 定义
- `bluevk/BlueVK.h` - VkPipelineLayout 类型
- `utils/Hash.h` - MurmurHash
