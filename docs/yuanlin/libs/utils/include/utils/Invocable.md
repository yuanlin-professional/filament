# Invocable.h / Invocable.cpp

## 文件概述
仅可移动的通用函数包装器，类似 std::function 但支持 move-only 捕获。

## 核心类/结构体/函数
- **Invocable<R(Args...)>**: 函数包装器模板
  - 仅可移动，不可复制
  - 支持 lambda 和其他函数对象
  - `operator()`: 调用存储的函数
  - `operator bool`: 检查是否包含函数
- **InvocableBase**: 基类，提供调试输出支持

## 关键实现逻辑
- 使用类型擦除：通过 void* 存储函数对象，配合 deleter 和 invoker 函数指针
- 没有小缓冲区优化 (SBO)，所有函数对象都通过 new 分配
- 通过 `std::is_invocable_v` 约束模板参数（C++17）

## 依赖关系
- `utils/ostream.h` (调试输出)
