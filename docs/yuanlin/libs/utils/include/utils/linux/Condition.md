# linux/Condition.h / linux/Condition.cpp

## 文件概述
Linux/Android 平台的条件变量实现，基于 futex 系统调用优化。

## 核心类/结构体/函数
- **Condition**: 基于 futex 的高效条件变量
  - `notify_one()` / `notify_all()`: 通知等待线程
  - `wait()`: 等待条件满足

## 关键实现逻辑
- 使用 futex 系统调用代替 pthread 条件变量，减少系统调用开销
- 与 linux/Mutex 配合使用

## 依赖关系
- `utils/linux/Mutex.h`, `src/linux/futex.h`
