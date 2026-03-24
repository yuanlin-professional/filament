# linux/Mutex.h / linux/Mutex.cpp

## 文件概述
Linux/Android 平台的互斥锁实现，基于 futex 系统调用优化。

## 核心类/结构体/函数
- **Mutex**: 基于 futex 的高效互斥锁

## 关键实现逻辑
- 使用原子操作 + futex 实现，无竞争时避免系统调用
- 比 pthread_mutex 更轻量

## 依赖关系
- `src/linux/futex.h`
