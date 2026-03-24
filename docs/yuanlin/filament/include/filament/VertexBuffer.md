# VertexBuffer.h

## 文件概述

定义顶点缓冲区，用于存储定义可渲染对象几何形状的顶点属性数据（位置、法线、UV 等）。支持多缓冲区交错布局和 BufferObject 共享。

## 核心类/结构体

- **`VertexBuffer`** - 顶点缓冲区，继承自 `FilamentAPI`。
- **`VertexBuffer::Builder`** - 构建器。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::bufferCount(count)` | 设置缓冲区数量（最多 8） |
| `Builder::vertexCount(count)` | 设置顶点数量 |
| `Builder::attribute(attr, bufIdx, type, offset, stride)` | 配置顶点属性 |
| `Builder::normalized(attr, normalize)` | 设置属性是否归一化 |
| `Builder::enableBufferObjects(enable)` | 启用 BufferObject 模式 |
| `Builder::advancedSkinning(enable)` | 启用高级蒙皮模式 |
| `Builder::build(engine)` | 创建 VertexBuffer |
| `setBufferAt(engine, idx, buffer, offset)` | 上传顶点数据 |
| `setBufferObjectAt(engine, idx, bufferObject)` | 关联 BufferObject |
| `getVertexCount()` | 获取顶点数量 |

## 依赖关系

- `filament/FilamentAPI.h`, `filament/MaterialEnums.h`
- `backend/BufferDescriptor.h`, `backend/DriverEnums.h`
- `utils/compiler.h`, `utils/StaticString.h`

## 使用示例

```cpp
auto* vb = VertexBuffer::Builder()
    .vertexCount(4)
    .bufferCount(1)
    .attribute(VertexAttribute::POSITION, 0, VertexBuffer::AttributeType::FLOAT3)
    .attribute(VertexAttribute::UV0, 0, VertexBuffer::AttributeType::FLOAT2, 12)
    .build(*engine);
vb->setBufferAt(*engine, 0, BufferDescriptor(vertices, size));
```
