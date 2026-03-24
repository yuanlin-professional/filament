# Blackboard

## 文件概述

`Blackboard.h` / `Blackboard.cpp` 实现了帧图系统中的"黑板"机制，作为一个全局的键值存储，允许不同的渲染通道之间通过名称共享 `FrameGraphHandle` 资源句柄。

## 核心类/结构体

- **`Blackboard`**: 使用 `std::unordered_map<std::string_view, FrameGraphHandle>` 存储名称到资源句柄的映射。
  - `put()`: 按名称存储句柄
  - `get<T>()`: 按名称获取类型化的 `FrameGraphId<T>`
  - `remove()`: 按名称删除条目
  - `operator[]`: 插入或获取条目引用

## 关键实现逻辑

- 查找时若键不存在，`getHandle` 返回默认的空 `FrameGraphHandle`。
- `operator[]` 使用 `insert_or_assign`，确保即使键已存在也能返回引用。
- 模板方法 `get<T>` 将基础 `FrameGraphHandle` 静态转换为类型化的 `FrameGraphId<T>`。

## 依赖关系

- `fg/FrameGraphId.h` - `FrameGraphHandle` 定义
- `<string_view>`, `<unordered_map>` - 标准库容器
