# Chunk.h / Chunk.cpp

## 文件概述

定义 EIFF 二进制格式中所有数据块（Chunk）的抽象基类。每个 Chunk 持有一个 `ChunkType` 标识其类型，并通过虚函数 `flatten()` 将自身序列化到 `Flattener` 中。

## 核心类

### `Chunk`（抽象类）

- **构造函数**: `explicit Chunk(ChunkType type)` -- 受保护构造，子类必须指定块类型。
- **`getType()`**: 返回当前块的 `ChunkType` 枚举值。
- **`flatten(Flattener &f)`**: 纯虚函数，子类实现具体的序列化逻辑。
- **析构函数**: 虚析构，在 `.cpp` 中定义为 `= default`。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mType` | `ChunkType` | 块类型标识 |

## 关键实现逻辑

`Chunk` 是一个典型的接口类，自身不包含序列化数据。所有具体的序列化逻辑由子类（如 `SimpleFieldChunk`、`MaterialTextChunk` 等）实现。

## 依赖关系

- `Flattener.h` -- 序列化写入器
- `<filament/MaterialChunkType.h>` -- `ChunkType` 枚举定义
- `<vector>` -- 标准库
