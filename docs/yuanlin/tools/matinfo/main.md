# main.cpp (matinfo)

## 文件概述
MATINFO 是一个材质文件检查工具，用于查看 matc 编译生成的二进制材质文件的详细信息。支持打印 GLSL、SPIRV、Metal、WGSL 着色器代码，导出二进制 SPIR-V，以及启动 Web 调试服务器。

## 核心函数
- `main()` - 入口：解析参数 -> 读取材质文件 -> 解析 chunk -> 显示信息或执行特定操作
- `parseChunks()` - 核心分发函数：根据配置打印着色器/字典/材质信息
- `transpileSpirv()` - 使用 spirv-cross 将 SPIR-V 反编译为 GLSL-ES
- `analyzeSpirv()` - 对 SPIR-V 进行精度混合分析，标注可能的混合精度问题
- `disassembleSpirv()` - 使用 spirv-tools 反汇编 SPIR-V

## 关键实现逻辑
1. 支持 .inc 文本格式（十六进制字节数组）和二进制格式的材质文件
2. SPIR-V 分析功能检测 `RelaxedPrecision` 修饰的变量与未修饰变量混合运算
3. Web 服务器模式通过 `DebugServer` 提供交互式材质检查界面
4. 着色器字典打印功能用于查看共享的着色器代码片段

## 依赖关系
- `matdbg/` - 着色器提取、文本写入、调试服务器
- `filaflat/` - chunk 容器
- `spirv_glsl.hpp` / `spirv-tools` - SPIR-V 工具链
