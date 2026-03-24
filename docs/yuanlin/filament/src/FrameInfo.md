# FrameInfo.h / FrameInfo.cpp

## 文件概述

实现帧性能信息的收集和管理系统。通过 GPU 定时器查询（Timer Queries）和 Fence 同步机制，精确记录每帧的 CPU/GPU 时间开销、显示呈现时间、垂直同步信息等。

## 核心类/结构体/函数

### `details::FrameInfo` 结构体

基础帧信息：
- `gpuFrameDuration` -- GPU 帧持续时间
- `denoisedFrameTime` -- 去噪后的帧时间（中值滤波）
- `valid` -- 数据是否有效

### `FrameInfoImpl` 结构体

扩展的帧信息实现，包含完整的时间戳：
- `beginFrame` / `endFrame` -- 主线程帧开始/结束时间
- `backendBeginFrame` / `backendEndFrame` -- 后端线程帧开始/结束时间
- `gpuFrameComplete` -- GPU 完成渲染的时间
- `vsync` -- 垂直同步时间
- `displayPresent` / `presentDeadline` -- 显示呈现相关时间
- `fence` -- 用于检测 GPU 完成的 Fence 句柄

### `CircularQueue<T, CAPACITY>` 模板类

固定容量的环形队列，支持前端插入和后端弹出，基于原始存储和 placement new 实现。支持迭代器遍历。

### `FrameInfoManager` 类

帧信息管理的核心类：
- **`beginFrame(...)`** -- 帧开始时调用，创建新的帧信息条目，发起定时器查询，记录时间戳。
- **`endFrame(DriverApi&)`** -- 帧结束时调用，创建 Fence 并在后端线程异步等待 GPU 完成。
- **`getLastFrameInfo()`** -- 获取最近的帧信息。
- **`updateUserHistory(...)`** -- 更新用户可访问的帧历史记录。
- **`getFrameInfoHistory(size_t)`** -- 获取帧信息历史。

## 关键实现逻辑

- **定时器查询池**：使用 4 个定时器查询对象的池（`POOL_COUNT=4`），循环使用并异步读取结果。
- **中值滤波去噪**：`denoiseFrameTime` 对最近 N 帧的 GPU 时间进行中值滤波，消除异常帧时间的影响。
- **异步 GPU 完成检测**：通过 `AsyncJobQueue` 在独立线程上等待 GPU Fence 完成，避免阻塞主线程或后端线程。
- **Compositor Timing**：在支持的平台上查询合成器时间信息（呈现截止时间、刷新率等）。
- **帧呈现时间追踪**：通过 `queryFrameTimestamps` 查询实际的显示呈现时间。

## 依赖关系

- `details/Engine.h` -- 引擎内部实现
- `details/SwapChain.h` -- 交换链
- `filament/Renderer.h` -- 渲染器公共接口（`Renderer::FrameInfo`）
- `backend/Platform.h`、`backend/DriverEnums.h`、`backend/Handle.h` -- 后端接口
- `utils/AsyncJobQueue.h` -- 异步作业队列

## 被引用关系

由 `FRenderer` 在每帧的 `beginFrame`/`endFrame` 中使用，帧性能数据供自适应渲染质量调整和性能分析工具使用。
