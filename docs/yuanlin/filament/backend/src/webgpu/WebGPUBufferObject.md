# WebGPUBufferObject

## 文件概述

`WebGPUBufferObject` 是 `HwBufferObject` 的 WebGPU 实现，表示可用于多种用途（Uniform、顶点属性、存储等）的 GPU 缓冲区对象。继承自 `HwBufferObject` 和 `WebGPUBufferBase`。

## 核心类/结构体

- **`WebGPUBufferObject`** - 多功能 GPU 缓冲区
  - 继承 `HwBufferObject`（Filament 硬件抽象）和 `WebGPUBufferBase`（WebGPU 缓冲区基类）

## 关键实现逻辑

1. **用途映射**: 通过 `getBufferObjectUsage` 将 Filament 的 `BufferObjectBinding` 映射到 WebGPU 用途：
   - `VERTEX` -> `wgpu::BufferUsage::Vertex`
   - `UNIFORM` -> `wgpu::BufferUsage::Uniform`
   - `SHADER_STORAGE` -> `wgpu::BufferUsage::Storage`
2. **CopyDst 默认添加**: 所有缓冲区对象均包含 `wgpu::BufferUsage::CopyDst`，以支持后续的数据更新

## 依赖关系

- `WebGPUBufferBase.h` - 缓冲区基类
- `DriverBase.h` / `backend/DriverEnums.h` - Filament 驱动基础设施
