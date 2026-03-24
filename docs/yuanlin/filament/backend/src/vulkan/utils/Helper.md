# Helper (Helper.h)

## 文件概述

提供 Vulkan 后端常用的辅助工具函数，主要包括 VkRect2D/VkExtent2D 的比较操作以及对 Vulkan 枚举 API（`vkEnumerateXxx`）的模板封装。

## 核心类/结构体

本文件仅包含 `filament::backend::fvkutils` 命名空间下的内联函数和模板函数。

## 关键实现逻辑

### 几何比较函数
- `equivalent(VkRect2D, VkRect2D)` - 比较两个矩形区域是否完全相同（整数比较，无需 epsilon）
- `equivalent(VkExtent2D, VkExtent2D)` - 比较两个二维尺寸是否相同

### Vulkan 枚举辅助模板
为 `vkEnumerateXxx` 系列 API 提供类型安全的模板封装，自动处理「先查询数量，再获取数据」的两步调用模式：

- `enumerate(func)` - 无输入参数版本（如 `vkEnumerateInstanceLayerProperties`）
- `enumerate(func, inData)` - 单输入参数版本（如 `vkEnumeratePhysicalDevices(instance, ...)`）
- `enumerate(func, inDataA, inDataB)` - 双输入参数版本（如 `vkEnumerateDeviceExtensionProperties(device, layer, ...)`）

均返回 `FixedCapacityVector<OutType>`，错误时触发 POSTCONDITION 检查。

内部使用 `EXPAND_ENUM` 宏展开实现，宏在使用后立即 `#undef` 避免污染。

## 依赖关系

- `backend/DriverEnums.h` - 后端枚举定义
- `utils/bitset.h` - 位集合工具
- `utils/FixedCapacityVector.h` - 固定容量向量
- `utils/Panic.h` - 错误检查宏
- `bluevk/BlueVK.h` - Vulkan 函数指针类型
