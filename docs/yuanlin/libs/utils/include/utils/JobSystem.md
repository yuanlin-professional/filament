# JobSystem.h / JobSystem.cpp

## 文件概述
基于工作窃取的多线程任务调度系统，是 Filament 渲染引擎并行化的核心组件。

## 核心类/结构体/函数
- **JobSystem**: 任务调度器
  - `createJob()`: 创建任务（支持函数指针、成员指针、仿函数等多种形式）
  - `run()` / `runAndRetain()` / `runAndWait()`: 提交并执行任务
  - `waitAndRelease()`: 等待任务完成
  - `adopt()` / `emancipate()`: 将当前线程加入/移出线程池
  - `setThreadName()` / `setThreadPriority()`: 线程配置工具
- **Job**: 缓存行对齐的 64 字节任务对象，包含内联存储区域
- **jobs::parallel_for()**: 数据并行工具函数
- **jobs::CountSplitter**: 基于计数的分割策略

## 关键实现逻辑
- 每个线程拥有独立的 WorkStealingDequeue，push/pop 在本线程执行，其他线程可 steal
- Job 对象使用对象池分配，缓存行对齐避免伪共享
- parallel_for 使用递归二分分割，自动适应线程数量
- 引用计数管理 Job 生命周期

## 依赖关系
- `utils/Allocator.h`, `utils/architecture.h`, `utils/Condition.h`, `utils/Mutex.h`
- `utils/WorkStealingDequeue.h`, `utils/Slice.h`, `utils/memalign.h`, `utils/ostream.h`
