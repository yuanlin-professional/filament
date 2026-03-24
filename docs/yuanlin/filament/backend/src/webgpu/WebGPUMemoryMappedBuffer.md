# WebGPUMemoryMappedBuffer

## 文件概述

`WebGPUMemoryMappedBuffer` 是 `HwMemoryMappedBuffer` 的 WebGPU 实现，表示一个内存映射缓冲区的元数据描述。记录了映射的缓冲区对象句柄、偏移、大小和访问标志。

## 核心类/结构体

- **`WebGPUMemoryMappedBuffer`** (继承 `HwMemoryMappedBuffer`)
  - `bufferObject` - 关联的 `BufferObjectHandle`
  - `offset` / `size` - 映射范围
  - `access` - 访问权限标志 (`MapBufferAccessFlags`)

## 关键实现逻辑

- 纯数据结构，构造函数仅存储参数

## 依赖关系

- `DriverBase.h` / `backend/DriverEnums.h`
