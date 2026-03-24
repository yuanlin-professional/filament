# MaterialBuilder.h / MaterialBuilder.cpp

## 文件概述
Filament 材质编译器的核心入口文件。`MaterialBuilder` 提供构建器模式 API，让用户指定材质的所有属性（着色模型、混合模式、参数、着色器代码等），然后编译生成 `.filamat` 二进制包。这是 filamat 库最重要的公共接口。

## 核心类/结构体/函数
- **`MaterialBuilderBase`** - 基类，定义 Platform/TargetApi/TargetLanguage/Optimization 等枚举，管理代码生成排列组合
- **`MaterialBuilder`** - 主类，继承自 `MaterialBuilderBase`
  - 链式 API：`name()`、`material()`、`materialVertex()`、`shading()`、`blending()`、`parameter()`、`constant()`、`variable()` 等几十个设置方法
  - `build(JobSystem&)` - 执行编译，返回 `Package`
  - `Parameter` / `Output` / `Constant` / `PushConstant` - 内部数据结构
  - `PropertyList` - 材质属性列表（bool 数组）
- **`targetApiFromBackend()`** - 将后端类型转换为 TargetApi

## 关键实现逻辑
- `build()` 流程：`prepareToBuild()` -> `findAllProperties()` -> `runSemanticAnalysis()` -> `generateShaders()` -> 写入 ChunkContainer -> 序列化为 Package
- `prepareToBuild()` 构建每材质的 SamplerInterfaceBlock 和 BufferInterfaceBlock
- `generateShaders()` 使用 JobSystem 并行编译所有变体，生成 GLSL/SPIR-V/MSL/WGSL
- `findAllProperties()` 通过静态代码分析确定着色器使用了哪些材质属性
- 支持特性级别 0-3，自动调整变体过滤和代码生成

## 依赖关系
- `filamat/Package.h`、`filamat/Enums.h`
- `filament/MaterialEnums.h`、`filament/MaterialChunkType.h`
- `private/filament/*`（BufferInterfaceBlock、SamplerInterfaceBlock、Variant 等）
- `backend/DriverEnums.h`
- GLSLPostProcessor、ShaderGenerator、GLSLTools 等内部模块
