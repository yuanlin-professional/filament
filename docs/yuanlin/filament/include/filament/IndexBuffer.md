# IndexBuffer.h

## 文件概述

定义索引缓冲区，用于存储 VertexBuffer 中顶点的索引数据，支持 16 位和 32 位索引格式。

## 核心类/结构体

- **`IndexBuffer`** - 索引缓冲区 GPU 资源，继承自 `FilamentAPI`。
- **`IndexBuffer::Builder`** - 构建器。
- **`IndexBuffer::IndexType`** - 枚举：`USHORT`（16位）/ `UINT`（32位）。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::indexCount(count)` | 设置索引数量 |
| `Builder::bufferType(type)` | 设置索引类型（16/32位） |
| `Builder::build(engine)` | 创建 IndexBuffer |
| `setBuffer(engine, buffer, byteOffset)` | 上传索引数据 |
| `setBufferAsync(...)` | 异步版本的数据上传 |
| `getIndexCount()` | 获取索引数量 |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`, `backend/BufferDescriptor.h`
- `utils/compiler.h`, `utils/StaticString.h`

## 使用示例

```cpp
auto* ib = IndexBuffer::Builder()
    .indexCount(6)
    .bufferType(IndexBuffer::IndexType::USHORT)
    .build(*engine);
ib->setBuffer(*engine, BufferDescriptor(indices, 6 * sizeof(uint16_t)));
```
