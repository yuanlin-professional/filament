# ShaderInfo

## 文件概述

定义着色器信息结构体和查询函数，用于获取材质包中各着色器变体的元数据（模型、变体、管线阶段、偏移量）。

**源文件**: `include/matdbg/ShaderInfo.h`, `src/ShaderInfo.cpp`

## 核心类/结构体

### `ShaderInfo`
- `shaderModel` -- 着色器模型（Mobile/Desktop）
- `variant` -- 变体标识
- `pipelineStage` -- 管线阶段（Vertex/Fragment）
- `offset` -- 在材质块中的偏移量

### 全局函数
- **`getShaderCount(container, type)`**: 获取指定 Chunk 类型中的着色器数量。读取 Chunk 数据开头的 uint64 计数值
- **`getShaderInfo(container, info, chunkType)`**: 填充 ShaderInfo 数组。读取每个着色器的 model、variant、stage、offset。对于 Surface 域的材质，根据管线阶段过滤变体位（vertex 变体和 fragment 变体的有效位不同）

## 关键实现逻辑

`getShaderInfo` 在读取变体后会根据 MaterialDomain 进行变体过滤：Surface 域的顶点着色器使用 `filterVariantVertex`，片段着色器使用 `filterVariantFragment`。这确保返回的变体信息与实际使用一致。

## 依赖关系

- `CommonWriter.h` -- `read()` 辅助模板函数
- `filaflat/ChunkContainer.h`, `filaflat/Unflattener.h` -- 数据读取
- `filament/MaterialChunkType.h`, `filament/MaterialEnums.h` -- 类型定义
- `private/filament/Variant.h` -- Variant 过滤方法
