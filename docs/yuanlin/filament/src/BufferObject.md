# BufferObject.cpp

## 文件概述

`BufferObject` 类的公共 API 实现文件。该文件通过 `downcast` 模式将公共接口调用委托给内部实现类 `FBufferObject`，是 Filament 典型的 API 代理层实现。

## 核心类/结构体/函数

### `BufferObject` 类方法

- **`setBuffer(Engine&, BufferDescriptor&&, uint32_t byteOffset)`** -- 设置缓冲区数据，将数据传输到 GPU。通过 `downcast` 委托给内部实现。
- **`getByteCount()`** -- 返回缓冲区的字节大小。

## 关键实现逻辑

使用 Filament 标准的 `downcast` 宏将公共 API 类（`BufferObject`）的方法调用转发到内部实现类（`FBufferObject`）。这种模式将接口与实现分离，隐藏内部细节。

## 依赖关系

- `details/BufferObject.h` -- 内部实现类 `FBufferObject` 的声明
- `details/Engine.h` -- 引擎内部实现
- `FilamentAPI-impl.h` -- API 实现模板实例化

## 被引用关系

作为 Filament 公共 API 的一部分，由用户代码调用，用于管理 GPU 缓冲区对象的数据上传。
