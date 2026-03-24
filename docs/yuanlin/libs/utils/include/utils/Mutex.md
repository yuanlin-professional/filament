# Mutex.h

## 文件概述
互斥锁的平台适配层，根据平台选择不同实现。

## 核心类/结构体/函数
- **Mutex**: 互斥锁类（平台适配别名）

## 关键实现逻辑
- Android 平台使用 `linux/Mutex.h`（基于 futex 的优化实现）
- 其他平台使用 `generic/Mutex.h`（基于 `std::mutex`）

## 依赖关系
- `utils/linux/Mutex.h` 或 `utils/generic/Mutex.h`
