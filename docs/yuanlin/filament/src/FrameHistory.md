# FrameHistory.h

## 文件概述

定义帧历史记录数据结构，用于存储前几帧的渲染信息。主要支持时间抗锯齿（Temporal AA）和屏幕空间反射（SSR）等需要帧间数据的后处理效果。

## 核心类/结构体/函数

### `FrameHistoryEntry` 结构体

存储单帧的历史数据，包含两个子结构：

- **`TemporalAA taa`** -- TAA 相关数据：
  - `color` -- 历史颜色纹理（FrameGraphTexture）
  - `desc` -- 纹理描述符
  - `projection` -- 世界空间到裁剪空间的投影矩阵
  - `jitter` -- 子像素抖动偏移
  - `frameId` -- 帧 ID（用于 Halton 序列）
- **`ssr`** -- 屏幕空间反射数据：
  - `color` -- 历史颜色纹理
  - `desc` -- 纹理描述符
  - `projection` -- 投影矩阵

### `TFrameHistory<T, SIZE>` 模板类

固定大小的 FIFO 队列，用于管理帧历史记录。

- **`front()` / `back()`** -- 访问最新/最旧的帧历史。
- **`getCurrent()`** -- 获取当前帧的信息存储槽。
- **`getPrevious()`** -- 获取前一帧的信息。
- **`commit()`** -- 将当前帧推入队列，最旧的帧被丢弃。使用 `std::move_backward` 移动元素。

### 类型别名

- **`FrameHistory`** -- `TFrameHistory<FrameHistoryEntry, 1u>`，保留 1 帧历史。

## 关键实现逻辑

`commit()` 方法使用 `std::move_backward` 将队列中的元素向后移动一位，然后将当前帧数据移入队首。旧的当前帧数据被重置为默认值。注意：存储在帧历史中的资源句柄需要在 `commit` 之前由调用者手动销毁。

## 依赖关系

- `fg/FrameGraphId.h`、`fg/FrameGraphTexture.h` -- FrameGraph 纹理资源
- `math/mat4.h`、`math/vec2.h` -- 数学类型

## 被引用关系

被 `FView` 类使用，通过 `commitFrameHistory()` 方法在每帧结束时更新帧历史数据。TAA 和 SSR 后处理效果通过帧历史访问前几帧的渲染结果。
