# CyclicBarrier.h / CyclicBarrier.cpp

## 文件概述
循环屏障同步原语，多个线程在屏障点同步汇合。

## 核心类/结构体/函数
- **CyclicBarrier**: 循环屏障类
  - `await()`: 阻塞直到所有线程到达屏障
  - `reset()`: 重置屏障并释放所有等待线程
  - `getThreadCount()`: 获取参与同步的线程数
  - `getWaitingThreadCount()`: 获取当前等待中的线程数

## 关键实现逻辑
- 使用两阶段状态机 (TRAP/RELEASE) 实现可重用屏障
- 最后一个到达的线程切换状态为 RELEASE 并唤醒所有线程
- 等所有线程释放完毕后切回 TRAP 状态，实现循环使用

## 依赖关系
- `utils/Condition.h`, `utils/Mutex.h`
