# ChunkContainer

## 文件概述

`ChunkContainer` 是 filaflat 库的核心容器类，用于解析和管理材质包（Material Package）中的二进制数据块（Chunk）。材质包由多个 Chunk 组成，每个 Chunk 有类型标识和大小。此类提供按类型 ID 快速查找和访问各个 Chunk 的能力。

**源文件**: `include/filaflat/ChunkContainer.h`, `src/ChunkContainer.cpp`

## 核心类/结构体

### `ChunkContainer`
- **构造函数**: `ChunkContainer(void const* data, size_t size)` -- 接收原始二进制数据指针和大小
- **`parse()`**: 解析整个数据，将所有 Chunk 映射到内部哈希表中。若发现不完整或大小异常的 Chunk 则返回 false
- **`hasChunk(Type)`**: 检查是否包含指定类型的 Chunk
- **`getChunkRange(Type)`**: 返回指定类型 Chunk 数据范围的 `[start, end)` 指针对
- **`getChunkCount()` / `getChunk(index)`**: 按索引遍历所有 Chunk

### 类型别名
- `ShaderContent = utils::FixedCapacityVector<uint8_t>` -- 着色器内容的二进制数据
- `BlobDictionary = utils::FixedCapacityVector<ShaderContent>` -- 二进制数据块字典

## 关键实现逻辑

`parse()` 方法通过 `Unflattener` 顺序读取每个 Chunk 的类型（uint64）和大小（uint32），并做边界检查确保 Chunk 不会越过数据末尾。合法的 Chunk 被存入 `tsl::robin_map` 中，键为 `ChunkType`，值为 `ChunkDesc`（起始指针 + 大小）。

## 依赖关系

- `filaflat/Unflattener.h` -- 用于顺序读取二进制数据
- `filament/MaterialChunkType.h` -- 定义 `ChunkType` 枚举
- `utils/FixedCapacityVector.h` -- 固定容量的向量容器
- `tsl/robin_map.h` -- 高性能哈希映射
