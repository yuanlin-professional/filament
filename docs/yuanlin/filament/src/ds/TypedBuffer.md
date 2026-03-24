# TypedBuffer

## 文件概述

`TypedBuffer.h` 提供了一个类型安全的 CPU 端缓冲区模板，用于管理 uniform buffer 的本地数据副本。

## 核心类/结构体

- **`TypedBuffer<T, N>`**: 模板类，在 CPU 侧存储 N 个类型为 T 的元素。
  - `mBuffer[N]`: 实际数据存储
  - `mSomethingDirty`: 脏标志，标记数据是否被修改
  - `itemAt(i)`: 获取第 i 个元素的引用（同时设置脏标志）
  - `edit()`: 获取第 0 个元素的引用（等同于 `itemAt(0)`）
  - `getSize()`: 返回字节大小（`sizeof(T) * N`）
  - `toBufferDescriptor()`: 将数据复制到驱动分配的内存并返回 `BufferDescriptor`

## 关键实现逻辑

- 访问任何元素都会自动标记为脏，确保后续 commit 时上传到 GPU。
- `toBufferDescriptor` 通过 `driver.allocate()` 分配临时内存，使用 `memcpy` 内联复制数据，并自动清除脏标志。
- 支持部分上传（指定 offset 和 size）。

## 依赖关系

- `private/backend/DriverApi.h` - 驱动 API（`allocate` 方法）
- `backend/BufferDescriptor.h` - 缓冲区描述符
