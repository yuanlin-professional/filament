# VulkanSemaphore (VulkanSemaphore.h / VulkanSemaphore.cpp)

## 文件概述

VkSemaphore 的引用计数封装。当 VulkanSemaphore 被销毁时，底层 VkSemaphore 不会被立即销毁，而是通过 VulkanSemaphoreManager 回收到池中供复用。

## 核心类/结构体

### `VulkanSemaphore` 结构体
继承自 `fvkmemory::Resource`：
- `VulkanSemaphore(manager, semaphore)` - 构造时绑定管理器和底层信号量
- `~VulkanSemaphore()` - 析构时将 VkSemaphore 归还管理器
- `getVkSemaphore()` - 获取底层 VkSemaphore 句柄

## 关键实现逻辑

- **池化回收**: 析构时调用 `mManager->recycle(mSemaphore)` 将信号量归还池，而非直接销毁
- **引用计数**: 作为 Resource 子类，通过 resource_ptr 自动管理生命周期

## 依赖关系

- `memory/Resource.h` - fvkmemory::Resource 基类
- `bluevk/BlueVK.h` - VkSemaphore 类型
