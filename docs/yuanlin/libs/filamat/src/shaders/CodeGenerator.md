# CodeGenerator.h / CodeGenerator.cpp

## 文件概述
GLSL 代码生成器，负责将材质属性、变体信息、uniform 块、采样器等转换为实际的 GLSL 着色器代码片段。是着色器生成管线的核心组件。

## 核心类/结构体/函数
- **`CodeGenerator`** - 代码生成器，根据 ShaderModel/TargetApi/TargetLanguage/FeatureLevel 生成适配的代码
  - `generateCommonProlog()` - 生成着色器头部（#version、#extension、#define、precision、特化常量等）
  - `generateUniforms()` / `generateBufferInterfaceBlock()` - 生成 UBO/SSBO 定义
  - `generateCommonSamplers()` - 生成采样器声明（带 layout binding）
  - `generateSurfaceLit()` / `generateSurfaceUnlit()` - 生成光照/非光照着色代码
  - `generateSurfaceMain()` - 生成 main() 函数
  - `generateMaterialProperty()` - 生成属性 #define
  - `generateSpecializationConstant()` - 生成特化常量
  - `generatePushConstants()` - 生成推送常量结构体

## 关键实现逻辑
- `generateCommonProlog()` 根据平台和 API 目标生成不同的 #version 和 #extension 指令
- `generateBufferInterfaceBlock()` 根据对齐方式（std140/std430）和目标（UBO/SSBO）生成正确的 layout 修饰符
- `generateSurfaceLit()` 按着色模型（Standard/Subsurface/Cloth）选择不同的 BRDF 代码
- 所有预编译的着色器片段通过 `SHADERS_*_DATA` 常量引入（由构建系统从 .glsl 文件生成）

## 依赖关系
- `MaterialInfo.h`、`UibGenerator.h`
- `filamat/MaterialBuilder.h`
- `private/filament/*`（EngineEnums、Variant、SamplerInterfaceBlock 等）
- `generated/shaders.h`（预编译着色器片段）
