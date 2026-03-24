# SharedHandle.h

## 文件概述
SharedHandle 是一个类似 shared_ptr 的模板类，专门用于管理 Filament 后端 Handle 类型的共享所有权。它通过引用计数跟踪句柄生命周期，在最后一个引用释放时通过 Deleter 函数销毁 GPU 资源。

## 核心类/结构体/函数
- **SharedHandle\<T, Deleter\>** - 共享句柄模板
  - 构造函数 - 接受 `Handle<T>` 和 Deleter 参数，创建控制块
  - 拷贝构造/赋值 - 增加引用计数
  - 移动构造/赋值 - 转移所有权（swap）
  - `operator Handle<T>()` - 隐式转换为原始句柄
  - `operator bool()` - 检查句柄是否有效
  - `clear()` - 释放引用
- **ControlBlock** - 内部控制块
  - `count` - 引用计数（int32_t）
  - `handle` - 原始句柄
  - `deleter` - 删除器
  - `inc()` / `dec()` - 引用计数操作，dec 到 0 时调用 deleter 并 delete 控制块

## 关键实现逻辑
- **非线程安全**：当前实现不使用原子操作，仅支持单线程场景
- 控制块通过 `new` 分配在堆上，确保在 SharedHandle 移动后控制块地址不变
- 赋值操作遵循先增后减的安全顺序，避免自赋值问题
- 支持可变参数构造 Deleter，增加灵活性

## 依赖关系
- `backend/Handle.h` - 后端句柄类型

## 被引用关系
- `RenderPass` - 使用 `BufferObjectSharedHandle` 和 `DescriptorSetSharedHandle` 管理实例化资源
