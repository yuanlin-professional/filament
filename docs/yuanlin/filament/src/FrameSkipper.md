# FrameSkipper.h / FrameSkipper.cpp

## 文件概述

实现帧跳过（Frame Skipping）机制，用于防止 CPU 超前 GPU 太多帧。通过 GPU Fence 检测 GPU 是否已完成之前的帧，如果 GPU 落后则跳过当前帧的渲染。这有助于控制帧延迟并防止命令队列过度膨胀。

## 核心类/结构体/函数

### `FrameSkipper` 类

- **构造函数 `FrameSkipper(size_t latency = 2)`** -- 创建帧跳过器，`latency` 参数定义可接受的未完成帧数量（1-2），默认为 2。
- **`shouldRenderFrame(DriverApi&)`** -- 检查是否应渲染当前帧。返回 `false` 表示应跳过。通过检查最旧的 Fence 是否已信号来判断。
- **`submitFrame(DriverApi&)`** -- 提交帧后调用，销毁最旧的 Fence 并创建新的 Fence。
- **`frameSkipped()`** -- 当帧被跳过时调用（不调用 `submitFrame`）。
- **`skipNextFrames(size_t)`** -- 强制跳过接下来的 N 帧（调试用）。
- **`terminate(DriverApi&)`** -- 销毁所有待处理的 Fence。

### 常量

- **`MAX_FRAME_LATENCY = 2`** -- 最大帧延迟。Android 上选择 2 是因为更高的延迟会被 `BufferQueueProducer::dequeueBuffer()` 节流。

## 关键实现逻辑

- **Fence 环形缓冲**：使用 `std::array<FenceHandle, MAX_FRAME_LATENCY>` 存储延迟 Fence。`submitFrame` 时移除最旧的 Fence 并在末尾添加新的。
- **延迟控制**：`mLatency = clamp(latency, 1, MAX_FRAME_LATENCY) - 1` 决定 Fence 在数组中的偏移量。延迟为 1 时立即检查上一帧的 Fence；延迟为 2 时检查两帧前的 Fence。
- **非阻塞查询**：`shouldRenderFrame` 使用 `getFenceStatus` 进行非阻塞查询，如果 Fence 未就绪则返回 `false`，不会阻塞 CPU。

## 依赖关系

- `backend/Handle.h` -- GPU Fence 句柄
- `private/backend/DriverApi.h` -- 驱动 API（创建/销毁/查询 Fence）
- `backend/DriverEnums.h` -- `FenceStatus` 枚举

## 被引用关系

由 `FRenderer` 在帧渲染循环中使用。`beginFrame` 时调用 `shouldRenderFrame` 决定是否渲染，`endFrame` 时调用 `submitFrame` 记录 GPU 进度。
