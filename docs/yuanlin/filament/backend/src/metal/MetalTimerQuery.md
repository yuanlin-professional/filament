# MetalTimerQuery

## 文件概述

MetalTimerQuery 实现了 Metal 后端的 GPU 时间查询功能。通过 `MTLSharedEvent` 在命令缓冲区的特定位置插入时间戳信号，测量两个信号之间的 GPU 执行耗时。提供接口抽象和 noop 回退实现。

**源文件**: `MetalTimerQuery.h`, `MetalTimerQuery.mm`

## 核心类/结构体

- **`MetalTimerQueryInterface`** - 纯虚接口基类
  - `beginTimeElapsedQuery()` - 开始时间查询
  - `endTimeElapsedQuery()` - 结束时间查询
  - `getQueryResult()` - 获取查询结果（非阻塞）
- **`MetalTimerQueryFence`** - 基于 `MTLSharedEvent` 的实现（iOS 12+）
  - 使用 `MetalFence` 在命令流中插入信号点
  - 通过信号回调记录 CPU 端时间戳来近似 GPU 时间
- **`TimerQueryNoop`** - 空实现，`getQueryResult` 始终返回 false

## 关键实现逻辑

- **begin**: 创建 `MetalFence` 并编码到当前命令缓冲区，在信号回调中记录开始时间戳（`steady_clock::now`）到 `query->status->elapsed`
- **end**: 再次创建 `MetalFence` 并编码，信号回调中计算 `当前时间 - elapsed`（即两次信号之间的时间差），存入 `elapsed` 并设置 `available = true`
- **弱引用安全**: 信号回调通过 `weak_ptr<Status>` 捕获查询状态，避免 MetalTimerQuery 在回调执行前被销毁导致悬挂指针
- **Fence 生命周期**: `MetalFence` 通过 `new` 创建，在信号回调中 `delete`，确保在 GPU 信号之前保持存活

## 依赖关系

- `MetalHandles.h` - MetalFence、MetalTimerQuery 句柄类型
- `Metal/Metal.h` - MTLSharedEvent
