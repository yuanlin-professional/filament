# Resource (Resource.h / Resource.cpp)

## 文件概述

定义 Vulkan 后端资源管理系统的基础资源类型。每个从 HandleAllocator 分配的 Vulkan 对象都继承自 `Resource` 或 `ThreadSafeResource`，通过引用计数实现自动生命周期管理。当引用计数归零时，资源会被延迟销毁。

## 核心类/结构体

### `ResourceType` 枚举
定义所有可管理的资源类型（共 23 种），包括 BUFFER_OBJECT、TEXTURE、PROGRAM、SWAP_CHAIN、RENDER_TARGET、DESCRIPTOR_SET 等。用于类型安全的资源操作和调试输出。

### `Resource` 结构体
非线程安全的资源基类，使用位域紧凑存储：
- `resManager` - 指向 ResourceManager 的指针（8 字节）
- `id` - Handle ID（4 字节）
- `mCount` - 24 位引用计数
- `restype` - 7 位资源类型
- `mHandleConsideredDestroyed` - 1 位标记，用于检测释放后使用（use-after-free）

提供 `inc()`/`dec()` 引用计数操作，当计数归零时调用 `destroy()` 触发延迟销毁。

### `ThreadSafeResource` 结构体
线程安全版本的资源基类，使用 `std::atomic<uint32_t>` 实现原子引用计数。`dec()` 使用 `memory_order_acq_rel` 保证多线程下的正确性。仅用于 VulkanFence 和 VulkanTimerQuery 等需要跨线程访问的类型。

## 关键实现逻辑

- **引用计数模式**: 采用侵入式引用计数，资源自身维护计数器，通过 `resource_ptr` 智能指针自动管理
- **延迟销毁**: 引用计数归零时不直接释放，而是通过 `ResourceManager::destructLaterWithType()` 加入待回收队列
- **类型映射**: `getTypeEnum<D>()` 使用 `if constexpr` 在编译期将 C++ 类型映射到 ResourceType 枚举
- **use-after-free 检测**: `mHandleConsideredDestroyed` 标记用于在调试模式下检测已释放资源的非法访问

## 依赖关系

- `private/backend/HandleAllocator.h` - Handle ID 基类定义
- `utils/Mutex.h` - 互斥锁支持
- `vulkan/memory/ResourceManager.h` - 资源管理器（延迟销毁队列）
- `vulkan/VulkanHandles.h` - 所有具体 Vulkan 资源类型的定义（用于模板实例化）
