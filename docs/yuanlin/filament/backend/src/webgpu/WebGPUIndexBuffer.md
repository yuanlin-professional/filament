# WebGPUIndexBuffer

## 文件概述

`WebGPUIndexBuffer` 是 `HwIndexBuffer` 的 WebGPU 实现，表示存储索引数据的 GPU 缓冲区，用于索引绘制。

## 核心类/结构体

- **`WebGPUIndexBuffer`** (继承 `HwIndexBuffer` + `WebGPUBufferBase`)
  - `mIndexFormat` - WebGPU 索引格式（Uint16 或 Uint32）

## 关键实现逻辑

- 根据元素大小自动选择格式: 2 字节 -> `Uint16`，其他 -> `Uint32`
- 缓冲区用途固定为 `CopyDst | Index`

## 依赖关系

- `WebGPUBufferBase.h` - 缓冲区基类
- `DriverBase.h` - Filament 硬件抽象
