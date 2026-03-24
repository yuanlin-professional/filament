# ResourcePointer (ResourcePointer.h)

## 文件概述

定义 `resource_ptr<D>` 模板类，这是 Vulkan 后端中类似 `std::shared_ptr` 的引用计数智能指针。它是 VulkanDriver 与 HandleAllocator 之间的主要接口，所有从 HandleAllocator 分配的对象都通过 `resource_ptr` 持有和访问。

## 核心类/结构体

### `resource_ptr<D>` 模板结构体
侵入式引用计数智能指针，核心接口包括：

**静态工厂方法：**
- `make(resManager, handle, args...)` - 从已有句柄构造对象并返回 resource_ptr
- `construct(resManager, args...)` - 分配句柄 + 构造对象一步完成
- `cast(resManager, handle)` - 将句柄转换为 resource_ptr（含 use-after-free 检查）

**生命周期操作：**
- 拷贝构造/赋值 - 增加引用计数
- 移动构造/赋值 - 转移所有权（swap 语义）
- 析构 - 减少引用计数
- `get()` - 获取裸指针
- `id()` - 获取句柄 ID
- `operator->` - 解引用访问

## 关键实现逻辑

- **引用计数管理**: 构造时调用 `inc()`，析构时调用 `dec()`，拷贝赋值时先 dec 旧引用再 inc 新引用
- **类型安全**: 使用 `std::enable_if` 和 `std::is_base_of` 约束模板参数，确保只允许基类到派生类的转换
- **use-after-free 检测**: `cast()` 方法在转换时检查 `isHandleConsideredDestroyed()` 标志，违反时触发 PRECONDITION 错误
- **VulkanDriver 特殊访问**: 私有的 `inc()`/`dec()` 方法仅对 VulkanDriver 开放（友元），用于 create/destroy API 中的生命周期追踪。`dec()` 会先设置 `setHandleConsiderDestroyed` 标记再减引用

## 依赖关系

- `vulkan/memory/Resource.h` - Resource/ThreadSafeResource 基类定义
- `vulkan/memory/ResourceManager.h` - 资源分配与类型转换
- `backend/Handle.h` - Handle 模板类
- `utils/compiler.h` - 编译器工具宏
