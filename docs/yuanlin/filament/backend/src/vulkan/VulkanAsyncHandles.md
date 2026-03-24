# VulkanAsyncHandles (VulkanAsyncHandles.h / VulkanAsyncHandles.cpp)

## 文件概述

定义需要线程安全访问的 Vulkan 异步句柄类型，包括命令缓冲区围栏、Filament Fence 和定时器查询。这些对象可能从多个线程访问，因此使用互斥锁和条件变量进行同步。

## 核心类/结构体

### `VulkanCmdFence`
命令缓冲区围栏封装，使用 shared_mutex 实现读写分离同步：
- `setStatus()` - 设置围栏状态并通知等待者
- `getStatus()` - 获取当前状态（共享读锁）
- `wait()` - 等待围栏信号，内部状态机：VK_INCOMPLETE -> VK_NOT_READY -> VK_SUCCESS
- `resetFence()` - 重置 VkFence（独占写锁，防止与 vkWaitForFences 并发）
- `cancel()` - 取消等待
- `completed()` - 工厂方法创建已完成的围栏

### `VulkanFence`
Filament Fence 对象，继承 ThreadSafeResource。通过条件变量等待关联的 VulkanCmdFence 就绪。支持取消操作，取消时会通知所有等待线程。

### `VulkanSync`
同步对象，持有转换回调列表（用于将 Vulkan Fence 转为平台原生同步原语如 fd）。

### `VulkanTimerQuery`
时间戳查询对象，持有起始和停止查询索引。通过关联的 VulkanCmdFence 判断查询结果是否可用。

## 关键实现逻辑

- **三态围栏**: VK_INCOMPLETE（未提交）-> VK_NOT_READY（已提交未完成）-> VK_SUCCESS（完成）
- **读写锁分离**: `wait()` 使用 shared_lock 允许多线程并发等待；`resetFence()` 使用 unique_lock 独占访问
- **等待-提交同步**: 如果 wait 在 submit 之前调用，会先通过条件变量等待状态从 INCOMPLETE 变为 NOT_READY

## 依赖关系

- `bluevk/BlueVK.h` - VkFence 等 Vulkan 类型
- `DriverBase.h` - HwFence/HwTimerQuery/HwSync 基类
- `backend/DriverEnums.h` - FenceStatus 枚举
- `backend/Platform.h` - Sync 回调类型
- `vulkan/memory/Resource.h` - ThreadSafeResource 基类
