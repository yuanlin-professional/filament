# StaticVector (StaticVector.h)

## 文件概述

定义一个编译期固定容量、运行时可变大小的容器 `StaticVector`。用于 Vulkan 后端中需要避免动态分配但元素数量不固定的场景。

## 核心类/结构体

### `StaticVector<T, CAPACITY>` 模板类
基于 `std::array<T, CAPACITY>` 的封装，使用 `uint16_t mSize` 追踪当前有效元素数量：
- `push_back(item)` - 添加元素（断言不超过容量）
- `pop_back()` - 移除末尾元素
- `clear()` - 重置大小为 0
- `find(item)` - 线性查找
- `operator[]` - 下标访问（带边界检查）
- `begin()`/`end()` - 迭代器支持（end 基于 mSize）
- `data()` - 获取底层数组指针
- `size()` - 返回当前大小

## 关键实现逻辑

- **不可拷贝但可移动**: 删除拷贝构造/赋值，提供移动语义（通过 swap 实现）
- **容量约束**: `static_assert` 确保 CAPACITY 不超过 uint16_t 最大值
- **零开销抽象**: 所有方法均为内联，底层直接使用 std::array，无动态分配
- **适用场景**: 在 VulkanCommands、VulkanHandles 等模块中用于存储数量有限的资源引用

## 依赖关系

- `utils/debug.h` - assert_invariant 断言宏
- `utils/Panic.h` - 错误处理
- `<array>` - 底层存储
