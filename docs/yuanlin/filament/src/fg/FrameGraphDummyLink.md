# FrameGraphDummyLink

## 文件概述

`FrameGraphDummyLink.h` 定义了一个无状态的虚拟帧图资源类型，用于在通道之间建立纯依赖关系而不涉及实际的纹理或缓冲资源。

## 核心类/结构体

- **`FrameGraphDummyLink`**: 一个最小化的帧图资源类型。
  - `Descriptor`: 空结构体
  - `SubResourceDescriptor`: 空结构体
  - `Usage`: `uint32_t` 类型
  - `DEFAULT_R_USAGE = 0x1`, `DEFAULT_W_USAGE = 0x2`

## 关键实现逻辑

- 该类型不对应任何实际的 GPU 资源，其 `ResourceAllocator` 特化（在 `ResourceAllocator.h` 中）的 `create` 和 `destroy` 方法都是空操作。
- 主要用途是在帧图中建立通道间的执行顺序依赖。

## 依赖关系

- `<cstdint>` - 标准整数类型
