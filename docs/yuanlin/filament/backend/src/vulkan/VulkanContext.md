# VulkanContext (VulkanContext.h / VulkanContext.cpp)

## 文件概述

存储 Vulkan 后端的不可变上下文信息，包括物理设备属性、支持的特性、内存属性、深度格式列表等。VulkanContext 在平台初始化阶段填充，之后作为只读数据在整个后端中共享。

## 核心类/结构体

### `VulkanAttachment` 结构体
渲染附件抽象，封装纹理引用及 mip 级别/层信息，提供获取 VkImage、VkFormat、VulkanLayout、VkExtent2D、VkImageView 的便捷方法。

### `VulkanRenderPassContext` 结构体
渲染通道执行上下文，缓存当前命令缓冲区、渲染目标、渲染通道、参数和子通道索引。

### `VulkanContext` 结构体
核心上下文数据，包含：
- `selectMemoryType()` - 静态方法，根据内存类型位掩码和属性要求选择合适的内存类型
- 物理设备属性和特性（VkPhysicalDeviceProperties2、VkPhysicalDeviceFeatures2）
- 内存属性（VkPhysicalDeviceMemoryProperties）
- 深度格式列表（attachment 和 blittable 两种）
- 扩展支持标志（debugUtils、debugMarkers、dynamicRendering、protectedMemory 等）
- 驱动配置标志（asyncPipelineCachePrewarming、parallelShaderCompile 等）
- Fence 导出标志

## 关键实现逻辑

- **内存类型选择**: `selectMemoryType()` 遍历所有内存类型，找到同时满足类型位掩码和属性标志的第一个
- **PIMPL 模式**: VulkanPlatform 通过 `mImpl->mContext` 间接持有 VulkanContext
- **特性链**: 使用 pNext 链查询多个 Vulkan 扩展特性（动态渲染、可移植性子集、全局优先级等）

## 依赖关系

- `vulkan/utils/Image.h` - VulkanLayout 枚举
- `vulkan/utils/Definitions.h` - VkFormatList 等类型
- `vulkan/memory/ResourcePointer.h` - resource_ptr 模板
- `bluevk/BlueVK.h` - Vulkan 类型
- `utils/bitset.h` / `utils/Mutex.h` / `utils/Slice.h` - 工具类
