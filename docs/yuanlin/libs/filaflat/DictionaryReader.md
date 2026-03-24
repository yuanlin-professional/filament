# DictionaryReader

## 文件概述

`DictionaryReader` 负责从材质包中反序列化（unflatten）着色器字典数据。字典是一种压缩存储结构：多个着色器变体共享的文本行或二进制 Blob 被去重后存储在字典中，各变体通过索引引用字典条目。

**源文件**: `include/filaflat/DictionaryReader.h`, `src/DictionaryReader.cpp`

## 核心类/结构体

### `DictionaryReader`
- **`unflatten(container, dictionaryTag, dictionary)`**: 静态方法，根据字典类型标签从 ChunkContainer 中读取并填充 `BlobDictionary`

## 关键实现逻辑

根据 `dictionaryTag` 的不同类型采用不同的反序列化策略：

1. **`DictionarySpirv`**: SPIR-V 字典。先读取压缩方案标识（目前仅支持方案 1 即 SMOL-V），然后逐条读取压缩数据并用 `smolv::Decode` 解压为完整的 SPIR-V 二进制。读取前会执行 8 字节对齐跳过。
2. **`DictionaryMetalLibrary`**: Metal 预编译库字典。直接读取每个 Blob 的原始数据，同样需要 8 字节对齐。
3. **`DictionaryText`**: 文本字典（GLSL/MSL/ESSL/WGSL 等文本着色器）。逐条读取以 null 结尾的字符串，包含尾部 null 字符以便后续拼接。

## 依赖关系

- `filaflat/ChunkContainer.h` -- 访问材质包中的 Chunk 数据
- `filaflat/Unflattener.h` -- 二进制数据读取
- `smolv.h` -- SMOL-V 解压库（仅 Vulkan 后端）
