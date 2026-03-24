# IndexBuffer.cpp

## 文件概述

`IndexBuffer` 类的公共 API 实现文件。提供索引缓冲区的数据上传（同步和异步）以及查询功能。

## 核心类/结构体/函数

### `IndexBuffer` 类方法

- **`setBuffer(Engine&, BufferDescriptor&&, uint32_t byteOffset)`** -- 同步设置索引缓冲区数据。
- **`setBufferAsync(Engine&, BufferDescriptor&&, uint32_t byteOffset, CallbackHandler*, AsyncCompletionCallback, void*)`** -- 异步设置索引缓冲区数据，完成后通过回调通知。
- **`getIndexCount()`** -- 返回索引数量。
- **`isCreationComplete()`** -- 检查缓冲区是否已创建完成。

## 关键实现逻辑

标准 `downcast` 代理模式。异步版本 `setBufferAsync` 支持回调处理器和完成回调，适用于大数据量的非阻塞上传场景。

## 依赖关系

- `details/IndexBuffer.h` -- 内部实现类 `FIndexBuffer`
- `details/Engine.h` -- 引擎内部实现

## 被引用关系

被用户代码调用，用于将索引数据上传到 GPU。与 `VertexBuffer` 和 `RenderableManager` 配合使用来定义可渲染几何体。
