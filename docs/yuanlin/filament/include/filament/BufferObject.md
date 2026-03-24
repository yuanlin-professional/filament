# BufferObject.h

## 文件概述

定义通用 GPU 缓冲区对象，用于在多个 VertexBuffer 之间共享顶点数据，支持高效的缓冲区交换。

## 核心类/结构体

- **`BufferObject`** - 通用 GPU 数据缓冲区，继承自 `FilamentAPI`。目前仅用于顶点数据。
- **`BufferObject::Builder`** - 用于构建 BufferObject 的 Builder 模式类。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::size(byteCount)` | 设置缓冲区大小（字节） |
| `Builder::bindingType(type)` | 设置绑定类型（默认为 VERTEX） |
| `Builder::name(name)` | 设置调试名称 |
| `Builder::build(engine)` | 创建 BufferObject 对象 |
| `setBuffer(engine, buffer, offset)` | 异步拷贝数据到缓冲区，offset 须为 4 的倍数 |
| `getByteCount()` | 返回缓冲区大小（字节） |

## 依赖关系

- `filament/FilamentAPI.h`
- `backend/DriverEnums.h`, `backend/BufferDescriptor.h`
- `utils/compiler.h`, `utils/StaticString.h`

## 使用示例

```cpp
auto* bo = BufferObject::Builder()
    .size(1024)
    .build(*engine);

bo->setBuffer(*engine, BufferDescriptor(data, size));
engine->destroy(bo);
```
