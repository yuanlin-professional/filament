# Condition.h

## 文件概述
条件变量的平台适配层，根据平台选择不同实现。

## 核心类/结构体/函数
- **Condition**: 条件变量类（平台适配别名）

## 关键实现逻辑
- Android 平台使用 `linux/Condition.h`（基于 futex 的优化实现）
- 其他平台使用 `generic/Condition.h`（基于 `std::condition_variable_any`）

## 依赖关系
- `utils/linux/Condition.h` 或 `utils/generic/Condition.h`
