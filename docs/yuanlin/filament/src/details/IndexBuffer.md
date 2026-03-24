# IndexBuffer

## 文件概述

`FIndexBuffer` 是 `IndexBuffer` 公共 API 的私有实现类，管理 GPU 索引缓冲区。支持同步和异步两种创建模式，异步模式通过 `CountdownCallbackHandler` 跟踪创建完成状态。

## 核心类/结构体

### `FIndexBuffer`
- 继承自 `IndexBuffer`
- 持有 `backend::Handle<backend::HwIndexBuffer>` 句柄
- `mCreationComplete` 原子布尔值跟踪异步创建状态
- 支持 UINT 和 USHORT 两种索引类型

### `IndexBuffer::BuilderDetails`
- 索引数量 `mIndexCount`、索引类型 `mIndexType`
- 异步创建参数：`mAsynchronous`、回调处理器和回调函数

## 关键实现逻辑

- **异步创建** -- 使用 `CountdownCallbackHandler` 管理异步完成通知，创建完成时设置 `mCreationComplete`
- **setBuffer()** -- 更新索引数据，要求偏移量 4 字节对齐
- **setBufferAsync()** -- 异步数据更新，通过 `CallbackAdapter` 适配用户回调

## 依赖关系

- `details/AsyncHelpers.h` -- 异步回调辅助工具
- `details/Engine.h` -- 引擎驱动 API

## 与公共 API 的关系

直接对应 `filament::IndexBuffer`。通过 `IndexBuffer::Builder` 创建，用于定义渲染图元的索引数据。
