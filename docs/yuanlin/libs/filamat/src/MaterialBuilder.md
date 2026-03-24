# MaterialBuilder.cpp

## 文件概述

`MaterialBuilder` 是 Filament 材质编译系统的核心入口，实现了从材质参数配置到最终二进制材质包（`Package`）的完整编译流程。它采用 Builder 模式，提供丰富的链式 API 来配置材质属性，然后通过 `build()` 方法执行着色器生成、优化、压缩和序列化。

## 核心类

### `MaterialBuilderBase`
- **`init()` / `shutdown()`**: 全局初始化/关闭 GLSL 工具链，使用引用计数管理。
- **`prepare()`**: 根据平台（MOBILE/DESKTOP/ALL）和目标 API（OpenGL/Vulkan/Metal/WebGPU）构建代码生成排列组合（`mCodeGenPermutations`）。

### `MaterialBuilder`（继承自 `MaterialBuilderBase`）

**Builder 配置方法**（均返回 `*this` 支持链式调用）：
- 基础属性: `name()`、`material()`、`materialVertex()`、`shading()`、`blending()`
- 渲染状态: `culling()`、`depthWrite()`、`depthCulling()`、`colorWrite()`、`doubleSided()`
- PBR 特性: `specularAntiAliasing()`、`clearCoatIorChange()`、`refractionMode()`
- 参数声明: `parameter()`（Uniform/Sampler）、`constant()`、`buffer()`、`subpass()`
- 输出声明: `output()`、`variable()`、`require()`
- 编译选项: `optimization()`、`targetApi()`、`platform()`、`featureLevel()`、`quality()`

**核心编译方法**：
- **`build(JobSystem&)`**: 主编译入口，执行完整的材质编译流程。
- **`prepareToBuild(MaterialInfo&)`**: 构建 SamplerInterfaceBlock 和 BufferInterfaceBlock，填充 MaterialInfo。
- **`findAllProperties()`**: 通过 GLSL 静态分析查找材质中实际使用的属性。
- **`runSemanticAnalysis()`**: 对着色器代码进行语义分析（顶点/片段/计算）。
- **`generateShaders()`**: 多线程生成所有着色器变体，并压缩存入 ChunkContainer。
- **`writeCommonChunks()` / `writeSurfaceChunks()`**: 将材质元数据写入 EIFF 块。

## 关键实现逻辑

### 编译流程（`build()` 方法）
1. 验证前置条件（初始化检查、参数合法性）
2. `prepareToBuild()` -- 构建接口块，填充 MaterialInfo
3. `checkMaterialLevelFeatures()` -- 检查特性级别兼容性（采样器数量等）
4. `findAllProperties()` -- GLSL 静态分析确定使用的材质属性
5. `runSemanticAnalysis()` -- 着色器语义分析
6. 创建 `ChunkContainer`，写入公共和表面特定块
7. `generateShaders()` -- 多线程生成并压缩着色器变体
8. 序列化容器为 `Package`，计算并追加 CRC32 校验

### 着色器生成（`generateShaders()`）
- 使用 `JobSystem` 并行编译多个变体，第一个任务单独运行以规避 glslang 的线程安全问题
- 支持 GLSL、ESSL1、SPIR-V、MSL、WGSL 五种输出格式
- 编译结果通过互斥锁保护的共享列表收集
- 文本着色器使用 `LineDictionary` 行级去重压缩
- SPIR-V 着色器使用 `BlobDictionary` 去重 + SMOL-V 压缩
- 变体按 (shaderModel, variant, stage) 排序后写入

### EIFF 序列化
最终二进制包格式：`[Chunk1][Chunk2]...[CRC32Chunk]`，其中每个 Chunk 为 `[Type(8B)][Size(4B)][Data...]`。CRC32 块作为最后一个块附加。

## 依赖关系

- `eiff/` 子目录 -- 所有 EIFF 序列化类（ChunkContainer、各种 Chunk）
- `GLSLPostProcessor.h` -- GLSL 后处理（优化、SPIR-V 编译、交叉编译）
- `shaders/ShaderGenerator.h` -- 着色器代码生成
- `sca/GLSLTools.h` -- GLSL 静态分析
- `MaterialVariants.h` -- 变体枚举
- `<private/filament/*>` -- Filament 内部接口块定义
- `<utils/JobSystem.h>` -- 多线程任务系统
- `<utils/Hash.h>` -- CRC32 计算
