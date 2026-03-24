# MaterialChunk

## 文件概述

`MaterialChunk` 管理材质包中的着色器索引和内容提取。材质包将所有着色器变体（按 ShaderModel、Variant、ShaderStage 三维组合）编码在一个 Chunk 中，`MaterialChunk` 负责建立索引并按需提取单个着色器的源码或二进制数据。

**源文件**: `include/filaflat/MaterialChunk.h`, `src/MaterialChunk.cpp`

## 核心类/结构体

### `MaterialChunk`
- **`initialize(materialTag)`**: 解析 Chunk 内的着色器索引表。读取着色器数量后，依次读取每个着色器的 model、variant、stage 和 offset，组合为 32 位 key 存入 `mOffsets` 哈希表
- **`getShader(...)`**: 根据 ShaderModel/Variant/Stage 提取着色器内容。根据 materialTag 的类型自动选择文本或二进制提取方式
- **`visitShaders(visitor)`**: 遍历所有着色器条目，调用 visitor 回调
- **`hasShader(...)`**: 检查是否存在指定的着色器变体
- **`decodeKey(key, ...)`**: 静态方法，将 32 位 key 解码回 ShaderModel/Variant/ShaderStage

## 关键实现逻辑

**Key 编码**: `(ShaderModel << 16) | (ShaderStage << 8) | Variant.key`，紧凑地将三维标识编码为一个 uint32。

**文本着色器提取 (`getTextShader`)**: 通过 offset 定位到着色器数据区域，读取着色器总大小和行数。每行存储为字典索引（uint16），从 BlobDictionary 中取出对应文本行并拼接成完整着色器源码。

**二进制着色器提取 (`getBinaryShader`)**: 直接通过 offset 作为字典索引，从 BlobDictionary 中复制整个二进制 Blob（如 SPIR-V 或 Metal Library）。

## 依赖关系

- `filaflat/ChunkContainer.h`, `filaflat/Unflattener.h` -- 数据读取
- `filament/MaterialChunkType.h` -- ChunkType 定义
- `private/filament/Variant.h` -- Variant 类型
- `backend/DriverEnums.h` -- ShaderModel、ShaderStage 枚举
- `tsl/robin_map.h` -- 着色器偏移量哈希表
