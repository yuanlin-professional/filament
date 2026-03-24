# GLSLPostProcessor.h / GLSLPostProcessor.cpp

## 文件概述
GLSL 后处理器，负责将生成的 GLSL 着色器编译为 SPIR-V，执行优化，并转译为目标语言（GLSL/MSL/WGSL）。是材质编译管线中的关键转换步骤。

## 核心类/结构体/函数
- **`GLSLPostProcessor`** - 着色器后处理器
  - `process()` - 主入口，接收 GLSL 输入，输出 GLSL/SPIR-V/MSL/WGSL
  - `spirvToMsl()` - SPIR-V 到 MSL 转译（使用 spirv-cross）
  - `spirvToWgsl()` - SPIR-V 到 WGSL 转译（使用 Tint）
  - `Config` - 编译配置（变体、目标API、着色器阶段、特性级别等）
- **辅助类型**：`SpirvBlob`、`DescriptorSets`、`DescriptorSetInfo`

## 关键实现逻辑
- 三种优化模式：NONE（直接输出）、PREPROCESSOR（预处理+SPIR-V）、SIZE/PERFORMANCE（完整优化）
- `fullOptimization()` 流程：GLSL -> glslang -> SPIR-V -> spvtools 优化 -> spirv-cross -> 目标语言
- MSL 生成时使用 Argument Buffer 模式，通过 `MetalArgumentBuffer` 替换 spirv-cross 生成的参数缓冲区定义
- `registerPerformancePasses()` / `registerSizePasses()` 注册不同的 SPIR-V 优化通道
- 特别处理 Metal 的半精度浮点、WebGPU 的采样器分离、ClipDistance 修正等

## 依赖关系
- glslang（GLSL 解析）、spirv-tools（SPIR-V 优化）、spirv-cross（转译）、Tint（WGSL）
- `filamat/MaterialBuilder.h`、`private/filament/Variant.h`
- `MetalArgumentBuffer.h`、`SpirvFixup.h`、`ShaderMinifier.h`
