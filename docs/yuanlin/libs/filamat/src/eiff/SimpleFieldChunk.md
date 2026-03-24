# SimpleFieldChunk.h / SimpleFieldChunk.cpp

## 文件概述

`SimpleFieldChunk` 是一个模板类，用于将单一简单值（如 bool、uint8/32/64、float、字符串指针）序列化为 EIFF 块。它是最轻量的 Chunk 子类，广泛用于写入材质的各种标量属性。

## 核心类

### `SimpleFieldChunk<T>`（继承自 `Chunk`，模板类）

- **构造函数**: `SimpleFieldChunk(ChunkType type, T value)` -- 指定块类型和要存储的值。
- **`flatten(Flattener& f)`**: 根据模板参数类型调用对应的 Flattener 写入方法。

### 模板特化（在 .cpp 中定义）

| 类型 | 写入方法 |
|------|---------|
| `uint8_t` | `f.writeUint8(t)` |
| `uint32_t` | `f.writeUint32(t)` |
| `uint64_t` | `f.writeUint64(t)` |
| `bool` | `f.writeBool(t)` |
| `const char*` | `f.writeString(t)` |
| `float` | `f.writeUint32(reinterpret_cast<uint32_t&>(t))` -- 将 float 的位模式作为 uint32 写入 |

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `t` | `T` | 要序列化的值 |

## 关键实现逻辑

`float` 类型通过 `reinterpret_cast` 将其内存表示作为 `uint32_t` 写入，这保留了浮点数的完整位模式。`ChunkContainer::emplace<T>()` 便捷方法专门用于创建此类型的块。

## 依赖关系

- `Chunk.h` / `Flattener.h` -- EIFF 基础设施
