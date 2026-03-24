# JsonWriter

## 文件概述

`JsonWriter` 负责将材质包中的信息序列化为 JSON 字符串，发送给 Web 调试客户端。包括材质属性信息和活跃变体信息。

**源文件**: `include/matdbg/JsonWriter.h`, `src/JsonWriter.cpp`

## 核心类/结构体

### `JsonWriter`
- **`writeMaterialInfo(package)`**: 生成材质的完整 JSON 描述，包括名称、版本、着色模型、光栅化状态、各后端（GLSL/ESSL1/Vulkan/Metal/WebGPU）的着色器列表、必需顶点属性等
- **`writeActiveInfo(package, shaderLanguage, shaderModel, activeVariants)`**: 生成活跃变体信息的 JSON 数组，格式为 `["backend", "shaderModel", variantIndex0, variantIndex1, ...]`
- **`getJsonString()` / `getJsonSize()`**: 获取最近生成的 JSON 字符串

## 关键实现逻辑

**属性输出**: 使用模板函数 `printChunk<T, V>()` 从 ChunkContainer 中读取指定类型的值并格式化为 JSON 键值对。分别有 Float、Uint32、String 的特化版本。

**着色器列表**: `printShaderInfo()` 为每个着色器变体输出 index、shaderModel、pipelineStage、variantString、variant key。`formatVariantString` 将 Variant 位掩码转为可读的缩写字符串（如 `DIR|DYN|SRE`）。

**活跃变体**: 根据着色器语言选择对应的 ChunkType，然后遍历 activeVariants 位图输出活跃的变体索引。

## 依赖关系

- `matdbg/ShaderInfo.h` -- ShaderInfo、getShaderCount、getShaderInfo
- `CommonWriter.h` -- read() 辅助函数、toString() 转换函数、formatVariantString
- `filaflat/ChunkContainer.h` -- 材质包数据访问
- `filament/MaterialEnums.h` -- 材质枚举类型
