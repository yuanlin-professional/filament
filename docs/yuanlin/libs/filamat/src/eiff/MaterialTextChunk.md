# MaterialTextChunk.h / MaterialTextChunk.cpp

## 文件概述

`MaterialTextChunk` 负责将文本格式的着色器条目（GLSL、ESSL1、Metal、WGSL）以字典压缩方式序列化为 EIFF 二进制块。每个着色器文本被编码为 `LineDictionary` 索引序列，实现高效存储。

## 核心类

### `MaterialTextChunk`（继承自 `Chunk`）

- **构造函数**: 接收 `TextEntry` 列表、`LineDictionary` 引用和 `ChunkType`。
- **`writeEntryAttributes(index, Flattener&)`**: 写入单个着色器条目的属性（shaderModel、variant、stage，各 1 字节）。

### 内部结构

- **`ShaderMapping`**: 记录着色器是否为重复项及其指向的原始条目索引。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEntries` | `vector<TextEntry>` | 着色器条目列表 |
| `mDictionary` | `const LineDictionary&` | 行级字典引用 |
| `mDuplicateMap` | `vector<ShaderMapping>` | 重复检测映射 |

## 关键实现逻辑

**`flatten()` 序列化格式**：
1. 标记偏移基准，写入着色器数量（uint64）
2. 写入每个条目的属性（3 字节）+ 偏移占位符（4 字节）
3. 对每个非重复着色器：回填偏移，调用 `compressShader()` 写入压缩数据

**`compressShader()` 压缩算法**：
1. 写入原始大小（uint32）和行数占位符
2. 逐行解析着色器文本，通过字典查找每行的索引列表
3. 将每个索引写为 uint16
4. 回填实际行数

**重复检测**：使用 `unordered_map` 对着色器文本进行去重，重复的着色器共享同一偏移，仅执行一次。检测结果缓存在 `mDuplicateMap` 中避免 DryRunner 和实际写入时重复计算。

## 依赖关系

- `Chunk.h` / `Flattener.h` / `LineDictionary.h` / `ShaderEntry.h` -- EIFF 基础设施
- `<utils/Log.h>` -- 错误日志
