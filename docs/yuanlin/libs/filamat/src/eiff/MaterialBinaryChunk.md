# MaterialBinaryChunk.h / MaterialBinaryChunk.cpp

## 文件概述

`MaterialBinaryChunk` 负责将二进制格式的着色器条目（主要是 SPIR-V）序列化为 EIFF 二进制块。与 `MaterialTextChunk` 不同，二进制着色器数据已预先存入 `BlobDictionary`，此处仅记录每个着色器的属性和字典索引。

## 核心类

### `MaterialBinaryChunk`（继承自 `Chunk`）

- **构造函数**: `MaterialBinaryChunk(const vector<BinaryEntry>&& entries, ChunkType type)` -- 接收二进制着色器条目列表和块类型（通常为 `MaterialSpirv`）。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEntries` | `const vector<BinaryEntry>` | 二进制着色器条目列表 |

## 关键实现逻辑

`flatten()` 序列化格式：
1. 写入条目数量（uint64）
2. 对每个条目写入：
   - `shaderModel`（uint8）
   - `variant.key`（uint8）
   - `stage`（uint8）
   - `dictionaryIndex`（uint32）-- 指向 `BlobDictionary` 中的索引

实际的二进制着色器数据由 `DictionarySpirvChunk` 负责写入，此块仅存储元数据和索引引用。

## 依赖关系

- `Chunk.h` / `ShaderEntry.h` -- EIFF 基础设施与着色器条目定义
