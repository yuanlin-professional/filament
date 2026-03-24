# generic/Condition.h

## 文件概述
通用平台的条件变量实现，基于 std::condition_variable_any 的简单封装。

## 核心类/结构体/函数
- **Condition**: 条件变量类，封装 `std::condition_variable_any`
  - `notify_one()` / `notify_all()`: 通知等待线程
  - `wait()`: 等待条件满足

## 关键实现逻辑
- 直接使用 C++ 标准库实现，适用于非 Android 平台

## 依赖关系
- 无外部 utils 依赖
