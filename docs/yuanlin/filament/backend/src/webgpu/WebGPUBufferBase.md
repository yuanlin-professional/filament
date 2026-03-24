# WebGPUBufferBase

## 文件概述

`WebGPUBufferBase` 是 WebGPU 缓冲区对象的基类，为创建和更新 GPU 缓冲区提供通用功能。它被 `WebGPUBufferObject`、`WebGPUIndexBuffer` 等类继承。

## 核心类/结构体

- **`WebGPUBufferBase`** - 缓冲区基类
  - `mBuffer` - 底层 `wgpu::Buffer` 对象（const，创建后不可更改引用）
  - `updateGPUBuffer()` - 通过暂存缓冲区将 CPU 数据上传到 GPU
  - `getBuffer()` - 返回底层缓冲区的 const 引用

## 关键实现逻辑

1. **对齐约束**: 创建缓冲区时自动对齐到 `FILAMENT_WEBGPU_BUFFER_SIZE_MODULUS`（4 字节），因为 WebGPU 的 `WriteBuffer` 要求写入大小为 4 的倍数
2. **暂存缓冲区模式**: `updateGPUBuffer` 使用 `WebGPUStagePool` 获取暂存缓冲区，将数据先写入暂存区再通过 `CopyBufferToBuffer` 拷贝到目标 GPU 缓冲区
3. **补零对齐**: 当数据大小不是 4 的倍数时，在暂存缓冲区末尾填充零字节，确保确定性行为

## 依赖关系

- `WebGPUConstants.h` - `FILAMENT_WEBGPU_BUFFER_SIZE_MODULUS` 常量
- `WebGPUQueueManager.h` - 获取命令编码器
- `WebGPUStagePool.h` - 暂存缓冲区池
- `backend/BufferDescriptor.h` - CPU 端缓冲区描述符
