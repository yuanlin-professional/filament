# MaterialCompiler.h / MaterialCompiler.cpp

## 文件概述
MATC 的材质编译器实现。继承自 `Compiler` 基类，负责读取 `.mat` 文件、解析材质定义、调用 `filamat::MaterialBuilder` 构建最终的材质包。

## 核心类
- `MaterialCompiler` - 实际的材质编译器
  - `run()` - 执行完整的编译流程
  - `checkParameters()` - 验证输入输出文件是否有效
  - `compileRawShader()` - 编译原始 GLSL 着色器为 SPIR-V 二进制

## 关键实现逻辑
1. 编译流程：读取输入 -> 解析 `#include` 指令 -> 模板替换 -> 解析材质属性 -> 构建着色器
2. 原始着色器模式：使用 glslang 将 GLSL 编译为 SPIR-V，再用 smolv 压缩
3. 支持 `--reflect` 参数反射模式（仅输出材质参数元数据 JSON，不实际构建）
4. 支持 `--output-format mat` 模式（仅输出 include 解析后的 .mat 文件）
5. 使用 `JobSystem` 多线程构建材质

## 依赖关系
- `filamat/MaterialBuilder` - 材质构建器
- `filament-matp/MaterialParser` - 材质文件解析器
- `glslang` / `smolv` - SPIR-V 编译和压缩
