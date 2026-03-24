# CompressedStringChunk.h / CompressedStringChunk.cpp

## 文件概述

`CompressedStringChunk` 使用 Zstandard (zstd) 压缩算法将字符串数据压缩后序列化为 EIFF 二进制块。用于存储材质源代码等较大的文本数据。

## 核心类

### `CompressedStringChunk`（继承自 `Chunk`）

- **构造函数**: `CompressedStringChunk(ChunkType type, string_view string, CompressionLevel level)` -- 指定块类型、待压缩字符串和压缩级别。

### `CompressionLevel` 枚举

| 值 | 说明 |
|---|------|
| `MIN` | 最低压缩级别（最快速度），映射到 `ZSTD_minCLevel()` |
| `MAX` | 最高压缩级别（最小体积），映射到 `ZSTD_maxCLevel()` |
| `DEFAULT` | 默认压缩级别，映射到 `ZSTD_defaultCLevel()` |

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mString` | `utils::CString` | 待压缩的字符串 |
| `mCompressionLevel` | `CompressionLevel` | 压缩级别 |

## 关键实现逻辑

`flatten()` 实现步骤：
1. 使用 `ZSTD_compressBound()` 计算压缩输出的上界并分配缓冲区
2. 调用 `ZSTD_compress()` 执行压缩
3. 检查是否有错误（`ZSTD_isError()`）
4. 将压缩后的数据通过 `Flattener::writeBlob()` 写入（先写 uint64 长度再写数据）

## 依赖关系

- `Chunk.h` / `Flattener.h` -- EIFF 基础设施
- `<utils/CString.h>` -- Filament 字符串类
- `<zstd.h>` -- Zstandard 压缩库
