# Utilities

## 文件概述

`details/Utilities.h` 定义了帧图系统内部使用的类型别名，统一了内存分配策略。

## 核心类/结构体

- **`Deleter<T, ARENA>`**: 自定义删除器，通过 Arena 的 `destroy` 方法释放对象。
- **`UniquePtr<T, ARENA>`**: `std::unique_ptr` 配合 Arena 删除器的别名。
- **`Allocator<T>`**: 基于 `LinearAllocatorArena` 的 STL 分配器别名。
- **`Vector<T>`**: 使用线性分配器的 `std::vector` 别名（32 字节大小）。

## 关键实现逻辑

- 所有帧图内部动态分配均通过线性分配器（`LinearAllocatorArena`）进行，具有极高的分配效率（接近栈分配速度）和整块释放特性。
- `Vector<T>` 在帧图中广泛用于存储 PassNode、ResourceNode 等列表。

## 依赖关系

- `Allocators.h` - `LinearAllocatorArena` 定义
- `<vector>`, `<memory>` - 标准库
