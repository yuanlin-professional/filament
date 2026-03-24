# Fence.cpp

## 文件概述

`Fence` 类的公共 API 实现文件。Fence 是 GPU 同步原语，用于等待 GPU 命令执行完成。提供阻塞等待和等待后销毁的功能。

## 核心类/结构体/函数

### `Fence` 类方法

- **`waitAndDestroy(Fence* fence, Mode mode)`** -- 静态方法，等待栅栏信号并销毁栅栏对象。委托给 `FFence::waitAndDestroy`。
- **`wait(Mode mode, uint64_t timeout)`** -- 等待栅栏信号，可指定超时时间。返回 `FenceStatus` 表示等待结果。

## 关键实现逻辑

通过 `downcast` 委托给内部实现类 `FFence`。`waitAndDestroy` 是一个便捷方法，将等待和销毁合并为单次操作。

## 依赖关系

- `details/Fence.h` -- 内部实现类 `FFence`

## 被引用关系

被 `FrameSkipper` 用于帧节流，被用户代码用于 CPU-GPU 同步场景，如确保 GPU 完成特定操作后再读回数据。
