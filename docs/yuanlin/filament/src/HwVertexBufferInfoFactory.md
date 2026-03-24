# HwVertexBufferInfoFactory.h / HwVertexBufferInfoFactory.cpp

## 文件概述

实现顶点缓冲区信息（Vertex Buffer Info）的工厂类，通过去重和引用计数管理 GPU 端的顶点缓冲区信息资源。相同的缓冲区计数、属性计数和属性配置组合会复用已有的 GPU 资源。

## 核心类/结构体/函数

### `HwVertexBufferInfoFactory` 类

- **`create(DriverApi&, uint8_t bufferCount, uint8_t attributeCount, AttributeArray attributes)`** -- 创建或复用顶点缓冲区信息。
- **`destroy(DriverApi&, Handle)`** -- 减少引用计数，归零时销毁。
- **`terminate(DriverApi&)`** -- 终止工厂。

### `Parameters` 结构体（132 字节）

- `bufferCount` -- 缓冲区数量
- `attributeCount` -- 属性数量
- `attributes` -- 属性数组（`backend::AttributeArray`）
- `hash()` -- 使用 `murmur3` 哈希对整个结构体进行哈希

## 关键实现逻辑

- **整体哈希**：由于 `Parameters` 结构体大小固定且对齐到 4 字节，直接使用 `murmur3` 对整个结构体内存进行哈希，效率很高。
- **整体比较**：相等性比较使用 `memcmp` 直接比较整个结构体的内存内容（包括 padding 字段初始化为 0 以保证一致性）。
- **大型内存池**：使用 2MB 的池分配器区域（由于 Key 为 136 字节较大，约支持 15K 条目）。

## 依赖关系

- `Bimap.h` -- 双向映射容器
- `backend/DriverApi.h`、`backend/DriverEnums.h` -- 后端接口（`AttributeArray`）
- `utils/Allocator.h`、`utils/Hash.h` -- 内存和哈希工具

## 被引用关系

由 `FEngine` 持有，在顶点缓冲区创建时调用，避免为相同的顶点属性布局重复创建 GPU 对象。
