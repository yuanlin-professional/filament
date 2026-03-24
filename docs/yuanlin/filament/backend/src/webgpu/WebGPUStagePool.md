# WebGPUStagePool

## 文件概述

`WebGPUStagePool` 是暂存缓冲区池，管理用于 CPU-GPU 数据传输的临时映射缓冲区。通过回收已完成使用的缓冲区避免频繁创建/销毁。

## 核心类/结构体

- **`WebGPUStagePool`**
  - `mBuffers` - 可用缓冲区（按大小排序的 multimap）
  - `mInProgress` - 正在使用中的缓冲区（与提交状态绑定）

## 关键实现逻辑

1. **获取缓冲区**: `acquireBuffer` 从池中查找大小 >= 需求的最小缓冲区；若无可用则创建新缓冲区
2. **回收缓冲区**: `recycleBuffer` 异步重新映射缓冲区为写模式，完成后放回池中
3. **垃圾回收**: `gc` 检查 `mInProgress` 中的缓冲区，仅回收关联提交已完成的缓冲区
4. **创建配置**: 新缓冲区使用 `MapWrite | CopySrc` 用途，`mappedAtCreation = true`

## 依赖关系

- `WebGPUConstants.h` - 日志宏
- `WebGPUQueueManager.h` - `WebGPUSubmissionState` 用于判断缓冲区使用完毕
