# DictionaryMetalLibraryChunk.h / DictionaryMetalLibraryChunk.cpp

## 文件概述

`DictionaryMetalLibraryChunk` 负责将 `BlobDictionary` 中的 Metal Library 二进制数据序列化为 EIFF 二进制块。与 `DictionarySpirvChunk` 不同，Metal Library 数据不进行额外压缩，直接以原始格式写入。

## 核心类

### `DictionaryMetalLibraryChunk`（继承自 `Chunk`）

- **构造函数**: `DictionaryMetalLibraryChunk(BlobDictionary&& dictionary)` -- 接管 Blob 字典。块类型固定为 `ChunkType::DictionaryMetalLibrary`。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mDictionary` | `BlobDictionary` | 二进制 Blob 字典 |
| `mStripDebugInfo` | `bool` | 头文件中声明但未使用 |

## 关键实现逻辑

`flatten()` 序列化格式：
1. 写入 `uint32` -- Blob 数量
2. 对每个 Blob：写入对齐填充（8 字节对齐），然后以 Blob 格式（uint64 长度 + 原始数据）写入

不使用额外压缩，因为 Metal Library 已经是编译后的二进制格式。

## 依赖关系

- `Chunk.h` / `Flattener.h` / `BlobDictionary.h` -- EIFF 基础设施
