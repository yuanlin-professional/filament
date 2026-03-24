# ShaderReplacer

## 文件概述

`ShaderReplacer` 用于在运行时替换材质包中指定着色器变体的源码。它是材质调试器实时编辑功能的核心实现，支持文本着色器（GLSL/MSL/WGSL）和二进制着色器（SPIR-V）的替换。

**源文件**: `include/matdbg/ShaderReplacer.h`, `src/ShaderReplacer.cpp`

## 核心类/结构体

### `ShaderReplacer`
- **构造函数**: 根据着色器语言设置 materialTag、deleteTag（ESSL1/ESSL3 共享字典，替换一个时需删除另一个）、dictionaryTag
- **`replaceShaderSource(...)`**: 主入口。解析原始包，克隆除字典和材质 Chunk 之外的所有 Chunk，然后用新内容重建字典和材质 Chunk
- **`getEditedPackage()` / `getEditedSize()`**: 获取编辑后的材质包数据
- **`getMaterialTag()`**: 获取当前操作的材质 Chunk 类型

### `ShaderIndex` (内部类)
文本着色器的索引数据库。从 ChunkContainer 中导入所有着色器文本，支持替换指定变体并重新导出为 DictionaryTextChunk + MaterialTextChunk。

### `BlobIndex` (内部类)
二进制着色器（SPIR-V）的索引数据库。管理 SMOL-V 压缩的 Blob 数据，支持替换指定变体并重新导出为 DictionarySpirvChunk + MaterialBinaryChunk。

## 关键实现逻辑

**文本着色器替换**: 克隆所有非目标 Chunk -> 从原始包构建 ShaderIndex -> 替换目标着色器 -> 使用 LineDictionary 重新编码所有着色器 -> 写出新的 Dictionary 和 Material Chunk。

**SPIR-V 替换** (`replaceSpirv`): 接收的源码可能是 GLSL 或 SPIR-V 反汇编文本。若为反汇编文本（以 `"; SPIR-V"` 开头），用 `spvTextToBinary` 汇编；否则作为 GLSL 用 glslang 编译为 SPIR-V。然后通过 BlobIndex 替换并重建 Chunk。

**排序**: `sortRecords` 确保编辑后的着色器顺序与原始包一致（按 shaderModel、variant.key、stage 排序），因为前端依赖此顺序。

## 依赖关系

- `filaflat/DictionaryReader.h`, `filaflat/MaterialChunk.h` -- 材质包读取
- `filamat/MaterialBuilder.h` -- targetApiFromBackend
- `eiff/ChunkContainer.h`, `eiff/DictionarySpirvChunk.h`, `eiff/DictionaryTextChunk.h`, `eiff/MaterialBinaryChunk.h`, `eiff/MaterialTextChunk.h`, `eiff/LineDictionary.h` -- Chunk 重建
- `GlslangToSpv.h` -- GLSL 到 SPIR-V 编译
- `sca/GLSLTools.h`, `sca/builtinResource.h` -- GLSL 编译辅助
- `spirv-tools/libspirv.h` -- SPIR-V 汇编
