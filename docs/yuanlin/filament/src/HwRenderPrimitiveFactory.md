# HwRenderPrimitiveFactory.h / HwRenderPrimitiveFactory.cpp

## 文件概述

实现渲染图元（Render Primitive）的工厂类，通过去重和引用计数管理 GPU 端的渲染图元资源。相同的顶点缓冲区、索引缓冲区和图元类型组合会复用已有的 GPU 资源。

## 核心类/结构体/函数

### `HwRenderPrimitiveFactory` 类

- **`create(DriverApi&, VertexBufferHandle, IndexBufferHandle, PrimitiveType)`** -- 创建或复用渲染图元。根据三个参数组合在 Bimap 中查找，已存在则增加引用计数。
- **`destroy(DriverApi&, Handle)`** -- 减少引用计数，归零时销毁 GPU 资源。
- **`terminate(DriverApi&)`** -- 终止工厂，断言所有资源已释放。

### `Parameters` 结构体（12 字节）

- `vbh` -- 顶点缓冲区句柄
- `ibh` -- 索引缓冲区句柄
- `type` -- 图元类型（三角形、线段等）
- `hash()` -- 使用 `hash::combine` 组合三个字段的哈希值

## 关键实现逻辑

- **去重策略**：通过 `(vbh, ibh, type)` 三元组作为键进行去重。使用组合哈希实现 O(1) 查找。
- **引用计数**：与 `HwDescriptorSetLayoutFactory` 相同的模式，通过 mutable `refs` 管理。
- **内存池**：使用 1MB 的池分配器区域，约支持 65K 个条目后回退到堆分配。

## 依赖关系

- `Bimap.h` -- 双向映射容器
- `backend/DriverApi.h`、`backend/Handle.h` -- GPU 资源管理
- `utils/Allocator.h`、`utils/Hash.h` -- 内存和哈希工具

## 被引用关系

由 `FEngine` 持有，在可渲染组件（Renderable）设置几何数据时调用，避免为相同的顶点/索引缓冲区组合重复创建 GPU 渲染图元。
