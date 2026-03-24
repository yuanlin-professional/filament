# WebGPURenderPrimitive

## 文件概述

`WebGPURenderPrimitive` 是 `HwRenderPrimitive` 的 WebGPU 实现，持有定义可渲染图元的顶点和索引缓冲区引用。纯头文件，无对应 .cpp 文件。

## 核心类/结构体

- **`WebGPURenderPrimitive`** (继承 `HwRenderPrimitive`)
  - `vertexBuffer` - 指向 `WebGPUVertexBuffer` 的裸指针
  - `indexBuffer` - 指向 `WebGPUIndexBuffer` 的裸指针

## 依赖关系

- `DriverBase.h` - `HwRenderPrimitive` 基类
