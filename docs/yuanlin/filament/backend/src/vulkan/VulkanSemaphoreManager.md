# VulkanSemaphoreManager (VulkanSemaphoreManager.h / VulkanSemaphoreManager.cpp)

## 文件概述

VkSemaphore 的池化管理器，维护信号量对象池以减少创建/销毁开销。与 VulkanSemaphore 配合实现自动回收机制。

## 核心类/结构体

### `VulkanSemaphoreManager` 类
信号量池管理器：
- `acquire()` - 从池中获取或新建一个 VulkanSemaphore（返回 resource_ptr）
- `terminate()` - 销毁池中所有 VkSemaphore
- `recycle(semaphore)` - 回收 VkSemaphore 到池中（由 VulkanSemaphore 析构时调用）

## 关键实现逻辑

- **池化策略**: 维护 `std::vector<VkSemaphore>` 作为空闲信号量池
- **按需创建**: 池空时通过 `vkCreateSemaphore` 创建新信号量
- **友元关系**: VulkanSemaphore 是友元，可直接调用私有的 `recycle()` 方法

## 依赖关系

- `vulkan/VulkanSemaphore.h` - VulkanSemaphore 定义
- `vulkan/memory/ResourceManager.h` / `ResourcePointer.h` - 资源管理
- `bluevk/BlueVK.h` - VkSemaphore 创建
