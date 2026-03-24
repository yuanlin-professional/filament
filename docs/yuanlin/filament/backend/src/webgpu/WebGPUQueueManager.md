# WebGPUQueueManager

## 文件概述

`WebGPUQueueManager` 管理 WebGPU 命令队列的提交和同步。同时定义了 `WebGPUSubmissionState` 用于追踪单次提交的完成状态。

## 核心类/结构体

- **`WebGPUSubmissionState`** - 提交状态追踪
  - `waitForCompletion(timeout)` - 等待提交完成
  - `setStatus(status)` - 设置完成状态并通知等待者
- **`WebGPUQueueManager`** - 队列管理器
  - `getCommandEncoder()` - 获取或创建当前命令编码器
  - `flush()` - 提交当前命令缓冲区
  - `finish()` - 提交并阻塞等待完成

## 关键实现逻辑

1. **延迟创建**: 命令编码器按需创建，每次 `getCommandEncoder` 检查是否存在
2. **提交回调**: `submit` 通过 `OnSubmittedWorkDone` 注册回调，在 GPU 完成时更新 `WebGPUSubmissionState`
3. **finish 轮询**: `finish` 通过循环调用 `ProcessEvents` + 1ms 睡眠等待 Dawn 回调系统推进内部序列号

## 依赖关系

- `utils/Panic.h` - 断言
- `webgpu/webgpu_cpp.h` - WebGPU C++ API
