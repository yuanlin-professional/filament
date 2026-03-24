# DictionarySpirvChunk.h / DictionarySpirvChunk.cpp

## 文件概述

`DictionarySpirvChunk` 负责将 `BlobDictionary` 中的 SPIR-V 二进制着色器数据使用 SMOL-V 压缩后序列化为 EIFF 二进制块。SMOL-V 是一种专门针对 SPIR-V 的高效压缩格式。

## 核心类

### `DictionarySpirvChunk`（继承自 `Chunk`）

- **构造函数**: `DictionarySpirvChunk(BlobDictionary&& dictionary, bool stripDebugInfo)` -- 接管 Blob 字典，指定是否剥离调试信息。块类型固定为 `ChunkType::DictionarySpirv`。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mDictionary` | `BlobDictionary` | 二进制 Blob 字典 |
| `mStripDebugInfo` | `bool` | 是否在 SMOL-V 编码时剥离 SPIR-V 调试信息 |

## 关键实现逻辑

`flatten()` 序列化格式：
1. 写入 `uint32(1)` -- 压缩方案版本（当前唯一支持 SMOL-V）
2. 写入 `uint32` -- Blob 数量
3. 对每个 Blob：写入对齐填充、使用 `smolv::Encode()` 压缩后以 Blob 格式（长度 + 数据）写入

SMOL-V 压缩利用了 SPIR-V 指令格式的规律性，能显著减小二进制着色器的体积。

## 依赖关系

- `Chunk.h` / `Flattener.h` / `BlobDictionary.h` -- EIFF 基础设施
- `<smolv.h>` -- SMOL-V SPIR-V 压缩库
