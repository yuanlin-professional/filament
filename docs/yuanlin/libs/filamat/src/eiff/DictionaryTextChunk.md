# DictionaryTextChunk.h / DictionaryTextChunk.cpp

## 文件概述

`DictionaryTextChunk` 负责将 `LineDictionary`（行级文本字典）序列化为 EIFF 二进制块。它是文本着色器压缩方案的字典部分，与 `MaterialTextChunk` 配合使用。

## 核心类

### `DictionaryTextChunk`（继承自 `Chunk`）

- **构造函数**: `DictionaryTextChunk(LineDictionary&& dictionary, ChunkType chunkType)` -- 接管字典所有权，指定块类型（通常为 `DictionaryText`）。
- **`getDictionary()`**: 返回内部字典的常量引用，供 `MaterialTextChunk` 在序列化时引用同一字典。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mDictionary` | `const LineDictionary` | 行级文本字典 |

## 关键实现逻辑

`flatten()` 序列化格式：
1. 写入 `uint32` -- 字典中字符串总数
2. 依次写入每个字符串（null 结尾格式）

这样反序列化时可以按索引顺序重建字典，供 `MaterialTextChunk` 的压缩着色器数据引用。

## 依赖关系

- `Chunk.h` / `Flattener.h` / `LineDictionary.h` -- EIFF 基础设施
- `<filament/MaterialChunkType.h>` -- 块类型枚举
