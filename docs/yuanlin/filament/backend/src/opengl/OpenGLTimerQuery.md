# OpenGLTimerQuery.h / OpenGLTimerQuery.cpp

## 文件概述

实现 OpenGL 后端的 GPU 时间查询功能，提供三种策略以适应不同硬件和驱动支持情况。

## 核心类/结构体

### `GLTimerQuery`
继承 `HwTimerQuery`，包含共享状态 `State`：GL 查询 ID、起始时间和原子的已用时间值。

### `TimerQueryFactory`
静态工厂，根据平台能力选择最佳的时间查询实现。

### `TimerQueryFactoryInterface`
基类接口，定义四个虚方法：`createTimerQuery`、`destroyTimerQuery`、`beginTimeElapsedQuery`、`endTimeElapsedQuery`。

### 三种实现策略

1. **TimerQueryNativeFactory**: 使用 `GL_TIME_ELAPSED` 查询（最准确），适用于支持 `EXT_disjoint_timer_query` 的设备。通过轮询 `GL_QUERY_RESULT_AVAILABLE` 异步获取结果。

2. **TimerQueryFenceFactory**: 使用平台围栏（fence）在异步线程中测量时间。在 `beginTimeElapsedQuery` 和 `endTimeElapsedQuery` 中各创建一个围栏，等待 GPU 完成后记录 CPU 时间差。

3. **TimerQueryFallbackFactory**: 纯 CPU 时间测量，仅在无其他方式可用时使用。

## 关键实现逻辑

- **工厂选择逻辑**: 优先使用原生查询，但如果驱动有 bug（`dont_use_timer_query`）则回退到围栏模式，最后回退到 CPU 时间。
- **异步结果获取**: Native 模式使用 `runEveryNowAndThen` 定期检查查询结果；Fence 模式使用 `AsyncJobQueue` 在后台线程等待围栏信号。
- **弱引用安全**: 使用 `weak_ptr<GLTimerQuery::State>` 防止查询对象被销毁后的悬挂访问。

## 依赖关系

- `GLUtils.h`, `OpenGLDriver.h`
- `backend/platforms/OpenGLPlatform.h`
- `utils/AsyncJobQueue.h`
