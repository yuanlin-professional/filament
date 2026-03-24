# vulkan_core.h

## 文件概述

`vulkan_core.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的核心头文件，定义了 Vulkan API 所有版本（1.0 至 1.3）的类型、常量、枚举、结构体和函数指针类型。这是 BlueVK 中最大的头文件，是整个 Vulkan 类型系统的基础。

## 核心类/结构体/函数

### 句柄类型
- **可分派句柄**: `VkInstance`、`VkPhysicalDevice`、`VkDevice`、`VkQueue`、`VkCommandBuffer`
- **不可分派句柄**: `VkSemaphore`、`VkFence`、`VkBuffer`、`VkImage`、`VkPipeline`、`VkRenderPass` 等

### 枚举类型
数百个枚举，包括 `VkResult`、`VkFormat`、`VkImageLayout`、`VkPipelineBindPoint`、`VkDescriptorType` 等。

### 结构体
数百个结构体，包括 `VkInstanceCreateInfo`、`VkDeviceCreateInfo`、`VkRenderPassCreateInfo`、`VkGraphicsPipelineCreateInfo` 等。

### 函数指针类型 (PFN_)
为每个 Vulkan 函数定义了对应的 `PFN_vkXxx` 类型，供动态加载使用。

## 关键实现逻辑

使用版本化的条件编译（`VK_VERSION_1_0`/`1_1`/`1_2`/`1_3`），每个版本段包含该版本新增的所有类型、常量和函数声明。版本号通过 `VK_MAKE_API_VERSION` 宏构造。

## 依赖关系

- `"vk_platform.h"` -- `VKAPI_ATTR`、`VKAPI_CALL`、`VKAPI_PTR` 宏及基础类型
