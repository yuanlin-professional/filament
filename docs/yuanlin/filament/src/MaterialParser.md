# MaterialParser.h / MaterialParser.cpp

## 文件概述
MaterialParser 负责从二进制材质文件中解析所有材质属性、着色器代码和接口块数据。它是材质加载流程的核心组件，支持多种着色器语言（ESSL3、ESSL1、MSL、WGSL、SPIRV、Metal Library）。

## 核心类/结构体/函数
- **MaterialParser** - 材质二进制文件解析器
  - `parse()` - 解析材质文件，选择最优着色器语言，初始化字典和着色器块
  - `computeCrc32()` / `getCrc32()` / `getPrecomputedCrc32()` - CRC32 校验和管理
  - `getShader()` - 根据着色器模型、变体和阶段获取着色器代码
  - `hasShader()` - 检查指定着色器变体是否存在
  - `getSourceShader()` - 获取 Zstd 压缩的源代码并解压
  - 大量属性访问器：`getName()`、`getUIB()`、`getSIB()`、`getBlendingMode()` 等
  - `operator==` - 通过内容比较两个 MaterialParser 是否等价
- **Chunk 解析结构体**：
  - `ChunkUniformInterfaceBlock` / `ChunkSamplerInterfaceBlock` - 接口块反扁平化
  - `ChunkDescriptorBindingsInfo` / `ChunkDescriptorSetLayoutInfo` - 描述符集布局
  - `ChunkMaterialConstants` / `ChunkMaterialPushConstants` - 常量和推送常量
- **MaterialParserDetails** - 内部实现细节（ManagedBuffer、ChunkContainer）

## 关键实现逻辑
- **语言选择**：`parse()` 按优先级列表遍历 `preferredLanguages`，选择材质文件中存在的第一种语言
- **内存管理**：`ManagedBuffer` 在构造时拷贝材质数据，确保 Parser 拥有独立的数据副本
- **CRC32 缓存**：使用 `std::atomic<uint32_t>` 缓存计算结果，避免重复计算
- **相等性比较**：先比较大小和 CRC32 快速排除，最后才做完整内存比较
- **着色器语言到 ChunkType 映射**：`shaderLanguageToTags()` 将着色器语言映射到材质块和字典块类型

## 依赖关系
- `filaflat/ChunkContainer.h` / `filaflat/MaterialChunk.h` - 扁平化容器
- `filaflat/DictionaryReader.h` / `filaflat/Unflattener.h` - 字典和反扁平化
- `zstd.h` - Zstd 解压（用于源代码）
- `private/filament/Variant.h` - 变体定义

## 被引用关系
- `MaterialDefinition` - 使用 MaterialParser 读取材质属性
- `MaterialCache` - 使用 MaterialParser 作为缓存键
- `LocalProgramCache` - 通过 MaterialParser 获取着色器代码
