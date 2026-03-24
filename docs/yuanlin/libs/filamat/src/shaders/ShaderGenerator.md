# ShaderGenerator.h / ShaderGenerator.cpp

## 文件概述
着色器生成器的高层协调器。组合 `CodeGenerator` 生成的各代码片段，加上用户提供的材质着色器代码，生成完整的顶点/片段/计算着色器源码。

## 核心类/结构体/函数
- **`ShaderGenerator`** - 着色器生成器
  - `createSurfaceVertexProgram()` - 生成表面材质的完整顶点着色器
  - `createSurfaceFragmentProgram()` - 生成表面材质的完整片段着色器
  - `createSurfaceComputeProgram()` - 生成计算着色器
  - `fixupExternalSamplers()` - 修复外部采样器（SPIR-V 优化后恢复 samplerExternalOES）

## 关键实现逻辑
每个 `create*Program()` 方法按固定顺序拼接着色器片段：prolog -> defines -> uniforms -> samplers -> getters -> user code -> lighting -> main。通过 `CodeGenerator` 的各种 `generate*()` 方法获取各部分代码，最后用 `appendShader()` 插入用户的材质代码。

## 依赖关系
- `MaterialInfo.h`、`CodeGenerator.h`、`UibGenerator.h`
- `filamat/MaterialBuilder.h`
- `private/filament/Variant.h`、`private/filament/EngineEnums.h`
