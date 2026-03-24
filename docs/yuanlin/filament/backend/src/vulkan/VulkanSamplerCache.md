# VulkanSamplerCache (VulkanSamplerCache.h / VulkanSamplerCache.cpp)

## 文件概述

VkSampler 对象的缓存管理器。Vulkan 中采样器是不可变对象，相同参数的采样器可以复用。本类使用哈希表缓存已创建的采样器，避免重复创建。

## 核心类/结构体

### `VulkanSamplerCache` 类
采样器缓存：
- `getSampler(params)` - 根据采样器参数查找或创建 VkSampler
- `terminate()` - 销毁所有缓存的 VkSampler

### `Params` 结构体（16 字节）
采样器缓存键值：
- `sampler` - Filament SamplerParams（过滤/寻址/比较等参数）
- `conversion` - VkSamplerYcbcrConversion 句柄（用于外部采样器）

## 关键实现逻辑

- **哈希缓存**: 使用 MurmurHash 对 16 字节 Params 进行哈希，robin_map 实现 O(1) 查找
- **自定义比较**: SamplerEqualTo 同时比较采样器参数和 YCbCr 转换句柄

## 依赖关系

- `backend/DriverEnums.h` - SamplerParams 定义
- `bluevk/BlueVK.h` - VkSampler 类型
- `tsl/robin_map.h` - 高性能哈希表
