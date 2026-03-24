# vk_layer.h

## 文件概述

`vk_layer.h` 定义了 Vulkan Loader 与 Layer（验证层、调试层等）之间的接口协议。该文件由 Khronos/LunarG 提供，用于 Vulkan Layer 开发者实现标准化的 Loader-Layer 交互。Vulkan Layer 是插入 Loader 与 ICD 之间的中间件，可用于参数验证、性能分析、调试等。

## 核心类/结构体/函数

### 接口版本常量
- **`CURRENT_LOADER_LAYER_INTERFACE_VERSION`**: 当前版本 2
- **`VK_CURRENT_CHAIN_VERSION`**: 链版本 1

### 版本协商
- **`VkNegotiateLayerStructType`**: 协商结构类型枚举
- **`VkNegotiateLayerInterface`**: 版本协商结构体，包含 Layer 版本号和三个核心函数指针（`pfnGetInstanceProcAddr`、`pfnGetDeviceProcAddr`、`pfnGetPhysicalDeviceProcAddr`）

### 实例/设备创建链
- **`VkLayerInstanceLink`**: 实例创建链的链表节点
- **`VkLayerDeviceLink`**: 设备创建链的链表节点
- **`VkLayerInstanceCreateInfo`**: 实例创建时的 Layer 信息结构
- **`VkLayerDeviceCreateInfo`**: 设备创建时的 Layer 信息结构

### 调用链结构
- **`VkEnumerateInstanceExtensionPropertiesChain`**: 扩展属性枚举调用链
- **`VkEnumerateInstanceLayerPropertiesChain`**: 层属性枚举调用链
- **`VkEnumerateInstanceVersionChain`**: 实例版本查询调用链

## 关键实现逻辑

Layer 机制通过链表将多个 Layer 串联，每个 Layer 持有下一级的 `GetInstanceProcAddr` 和 `GetDeviceProcAddr`。Loader 在创建 Instance/Device 时通过 `pNext` 链传递 `VkLayerInstanceCreateInfo`，Layer 从中获取链上下一节点的函数指针，实现拦截和转发。

## 依赖关系

- `"vulkan.h"` -- Vulkan 核心类型
