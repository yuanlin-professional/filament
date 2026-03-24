# AndroidFrameCallback

## 文件概述

`AndroidFrameCallback.h` / `AndroidFrameCallback.cpp` 实现了 Android 平台上基于 Choreographer 的帧回调机制。该类在专用线程上运行 Looper，通过 Choreographer 接收 VSync 信号和帧时间线数据，用于帧调度和展示时间预测。仅在 Android 13 (API 33) 及以上版本支持。

## 核心类/结构体

### `AndroidFrameCallback`
- **Timeline**: 内部结构体，包含 `frameTime`（帧时间）、`expectedPresentTime`（预期展示时间）和 `frameTimelineDeadline`（帧时间线截止时间），均为纳秒级时间戳。
- **init()**: 启动 Looper 线程，创建 Choreographer 实例并注册 VSync 回调。使用 `CountDownLatch` 同步线程初始化。
- **terminate()**: 请求退出、唤醒 Looper、释放资源并等待线程结束。
- **isSupported()**: 静态方法，检查当前设备是否支持（Android 33+）。
- **getPreferredTimeline()**: 线程安全地返回最新的首选时间线数据。

## 关键实现逻辑

- 使用独立线程运行 `ALooper`，通过 `ALooper_pollOnce` 阻塞等待事件。
- VSync 回调中通过 `AChoreographerFrameCallbackData` 获取首选帧时间线索引，提取展示时间和截止时间。
- 每次回调自动注册下一帧的回调（`AChoreographer_postVsyncCallback`），形成持续的帧同步循环。
- 使用 `mutex` 保护 `mPreferredTimeline` 的读写线程安全。

## 依赖关系

- `<android/choreographer.h>` / `<android/looper.h>` - Android NDK Choreographer 和 Looper API
- `utils::CountDownLatch` - 线程同步屏障
- `utils::Panic` / `utils::debug` - 断言和前置条件检查
- `utils::JobSystem` - 线程命名和优先级设置
- `private/utils/Tracing.h` - 性能追踪
