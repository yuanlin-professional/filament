# ResourceManager (ResourceManager.h / ResourceManager.cpp)

## 文件概述

Vulkan 后端的核心资源管理器，负责所有 GPU 资源句柄的分配、构造、类型转换和延迟回收。它封装了 `HandleAllocatorVK`，并提供线程安全的垃圾回收机制，确保资源在安全时机被销毁。

## 核心类/结构体

### `ResourceManager` 类
资源管理器，提供以下核心功能：
- `allocHandle<D>()` - 从 HandleAllocator 分配一个新句柄
- `construct<D, B>(handle, args...)` - 在句柄内构造对象并初始化 Resource 元数据
- `handle_cast<Dp, B>(handle)` - 将句柄转换为具体类型指针
- `destruct<D, B>(handle)` - 解构对象并释放句柄
- `gc()` - 执行垃圾回收，处理待销毁队列
- `terminate()` - 循环调用 gc() 直到所有资源释放完毕

## 关键实现逻辑

- **延迟销毁队列**: 维护两个 GcList（`mGcList` 和 `mThreadSafeGcList`），前者用于非线程安全类型，后者用于 Fence/TimerQuery 等需要跨线程的类型
- **线程安全分离**: `destructLaterWithType()` 根据 `isThreadSafeType()` 判断资源类型，将线程安全类型加入带互斥锁保护的队列
- **gc() 执行流程**: 先处理线程安全队列（持有锁），再交换主队列并处理。交换操作确保析构期间新增的待销毁项不会丢失
- **类型分发**: `destroyWithType()` 通过 switch-case 将 ResourceType 枚举映射回具体的模板析构调用
- **资源泄漏调试**: 编译时启用 `FVK_DEBUG_RESOURCE_LEAK` 后，会维护每种类型的计数器用于检测泄漏

## 依赖关系

- `vulkan/memory/Resource.h` - 资源基类和类型枚举
- `vulkan/VulkanAsyncHandles.h` - 异步句柄类型前向声明
- `vulkan/VulkanHandles.h` - 所有具体 Vulkan 句柄类型（用于 destroyWithType 的分发）
- `private/backend/HandleAllocator.h` - 底层句柄分配器 HandleAllocatorVK
- `utils/Panic.h` - 断言与错误处理
- `utils/Logger.h` - 日志输出（资源泄漏打印）
