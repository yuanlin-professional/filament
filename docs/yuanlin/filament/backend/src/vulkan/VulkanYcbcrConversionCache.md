# VulkanYcbcrConversionCache (VulkanYcbcrConversionCache.h / VulkanYcbcrConversionCache.cpp)

## 文件概述

VkSamplerYcbcrConversion 对象的缓存管理器。YCbCr 采样器转换用于从 YUV 格式图像（如 Android 相机输出）中正确采样颜色数据。

## 核心类/结构体

### `VulkanYcbcrConversionCache` 类
YCbCr 转换缓存：
- `getConversion(params)` - 根据参数查找或创建 VkSamplerYcbcrConversion
- `terminate()` - 销毁所有缓存的转换对象

### `Params` 结构体（16 字节）
转换缓存键值：
- `conversion` - SamplerYcbcrConversion 参数（模型、范围、色度偏移等，4 字节）
- `format` - VkFormat（4 字节）
- `externalFormat` - 外部格式标识符（8 字节，用于 Android 外部格式）

## 关键实现逻辑

- **哈希缓存**: 使用 MurmurHash 对 16 字节 Params 哈希，robin_map 实现 O(1) 查找
- **自定义比较**: ConversionEqualTo 同时比较转换参数、格式和外部格式
- **外部格式支持**: 当 `externalFormat != 0` 时，VkFormat 可能为 UNDEFINED，使用外部格式标识符

## 依赖关系

- `vulkan/utils/Definitions.h` - SamplerYcbcrConversion 定义
- `backend/DriverEnums.h` - 后端枚举
- `bluevk/BlueVK.h` - VkSamplerYcbcrConversion 类型
- `tsl/robin_map.h` - 哈希表
