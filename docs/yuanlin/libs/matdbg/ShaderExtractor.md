# ShaderExtractor

## 文件概述

`ShaderExtractor` 是从材质包中提取着色器源码的工具类。支持所有着色器语言（ESSL1/ESSL3/MSL/Metal Library/WGSL/SPIRV），并提供 SPIR-V 到 GLSL 的反编译和 SPIR-V 反汇编功能。

**源文件**: `include/matdbg/ShaderExtractor.h`, `src/ShaderExtractor.cpp`

## 核心类/结构体

### `ShaderExtractor`
- **构造函数**: 根据目标着色器语言设置对应的 materialTag 和 dictionaryTag（如 ESSL3 -> MaterialGlsl + DictionaryText，SPIRV -> MaterialSpirv + DictionarySpirv）
- **`parse()`**: 解析 ChunkContainer 并初始化 MaterialChunk
- **`getShader(shaderModel, variant, stage, shader)`**: 提取指定变体的着色器内容。先从字典读取 Blob 数据，再从 MaterialChunk 中获取着色器
- **`getDictionary(dictionary)`**: 直接获取字典数据
- **`spirvToGLSL(shaderModel, data, wordCount)`**: 静态方法，使用 spirv-cross 将 SPIR-V 反编译为 GLSL。Mobile 模型使用 ES 3.10 + mediump，Desktop 使用 GLSL 4.50
- **`spirvToText(data, wordCount)`**: 静态方法，使用 SPIRV-Tools 将 SPIR-V 反汇编为可读文本

## 关键实现逻辑

语言到 Chunk 类型的映射在构造函数中完成，确保一致性。`getShader` 每次调用都会重新解压字典，保证线程安全（但牺牲了性能）。

SPIR-V 反编译先验证二进制合法性（`spvValidateBinary`），然后使用缩进和友好命名选项进行反汇编。

## 依赖关系

- `filaflat/ChunkContainer.h`, `filaflat/DictionaryReader.h`, `filaflat/MaterialChunk.h` -- 材质包读取
- `spirv_glsl.hpp` -- SPIR-V 到 GLSL 反编译（spirv-cross）
- `spirv-tools/libspirv.h` -- SPIR-V 验证和反汇编
