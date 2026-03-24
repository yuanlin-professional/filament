# WorkStealingDequeue.h

## 文件概述
无锁固定大小工作窃取双端队列，是 JobSystem 的核心数据结构。

## 核心类/结构体/函数
- **WorkStealingDequeue<TYPE, COUNT>**: 工作窃取队列
  - `push(item)`: 主线程从 bottom 端压入任务
  - `pop()`: 主线程从 bottom 端弹出任务
  - `steal()`: 其他线程从 top 端窃取任务
  - COUNT 必须是 2 的幂

## 关键实现逻辑
- push/pop 由拥有者线程调用，steal 由其他线程调用
- 使用原子操作 + 内存序保证实现无锁并发
- top 和 bottom 使用 relaxed/acquire/release 内存序精细控制

## 依赖关系
- 无外部 utils 依赖
