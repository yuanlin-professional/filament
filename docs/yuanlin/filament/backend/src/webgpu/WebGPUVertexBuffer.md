# WebGPUVertexBuffer

## 文件概述

`WebGPUVertexBuffer` 是 `HwVertexBuffer` 的 WebGPU 实现，持有顶点数据的 GPU 缓冲区数组和对应的顶点布局信息句柄。

## 核心类/结构体

- **`WebGPUVertexBuffer`** (继承 `HwVertexBuffer`)
  - `mVertexBufferInfoHandle` - 关联的顶点布局信息句柄
  - `mBuffers` - GPU 缓冲区数组（`vector<wgpu::Buffer>`）

## 关键实现逻辑

- 构造时根据 `bufferCount` 预分配缓冲区数组大小
- 缓冲区的实际内容由驱动后续通过 `updateGPUBuffer` 填充

## 依赖关系

- `DriverBase.h` / `backend/Handle.h` - 句柄类型
