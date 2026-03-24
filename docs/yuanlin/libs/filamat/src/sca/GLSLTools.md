# GLSLTools.h / GLSLTools.cpp

## 文件概述
GLSL 静态代码分析工具集，用于验证材质着色器的语义正确性，以及通过 AST 分析自动检测着色器使用了哪些材质属性（Property）。

## 核心类/结构体/函数
- **`GLSLTools`** - 静态分析主类
  - `init()` / `shutdown()` - 初始化/关闭 glslang
  - `analyzeFragmentShader()` - 验证片段着色器（检查 material()/prepareMaterial()/surfaceShading() 函数）
  - `analyzeVertexShader()` - 验证顶点着色器（检查 materialVertex() 函数）
  - `analyzeComputeShader()` - 验证计算着色器
  - `findProperties()` - 通过 AST 分析检测着色器写入了哪些材质属性
  - `textureLodBias()` - 向 texture() 调用注入 LOD 偏移参数
  - `hasCustomDepth()` - 检测着色器是否写入 gl_FragDepth 或调用 discard
- **`GLSLangCleaner`** - RAII 类，管理 glslang 线程池分配器
- **`Symbol` / `Access`** - 符号跟踪数据结构，记录变量的访问链（结构体索引、函数调用等）

## 关键实现逻辑
- `findProperties()` 从 material() 函数入口开始，追踪 MaterialInputs 参数的所有写入操作
- `scanSymbolForProperty()` 识别 `material.baseColor = ...` 等赋值模式，将字段名映射为 Property 枚举
- `textureLodBias()` 遍历 AST 中所有 `EOpTexture` 节点，添加第三个参数（lodBias symbol）
- `getShadingLanguageVersion()` 根据 ShaderModel 和 FeatureLevel 返回 GLSL/ESSL 版本号

## 依赖关系
- glslang：`ShaderLang.h`、`localintermediate.h`
- `ASTHelpers.h`、`builtinResource.h`
- `filamat/Enums.h`、`filamat/MaterialBuilder.h`
