# src/linux/futex.h

## 文件概述
Linux futex 系统调用的底层封装。

## 核心类/结构体/函数
- **futex_wake()**: 唤醒等待在 futex 上的线程
- **futex_wait()**: 在 futex 上等待

## 关键实现逻辑
- 直接封装 Linux `futex()` 系统调用
- 为 linux/Mutex 和 linux/Condition 提供底层支持

## 依赖关系
- Linux syscall 接口
