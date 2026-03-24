# ExternalCompile.h / ExternalCompile.cpp

## 文件概述
MATEDIT 的外部编译模块。负责从已编译的材质文件中提取着色器，通过外部脚本将文本着色器（如 MSL）编译为二进制格式（如 .metallib），然后重新打包材质文件。

## 核心函数
- `externalCompile()` - 主函数：读取材质 -> 提取着色器 -> 调用外部脚本编译 -> 重新打包
- `compileMetalShaders()` - 遍历所有 MSL 着色器条目，逐个调用外部脚本编译为 .metallib
- `invokeScript()` - 通过 `fork()/execvp()` 调用用户提供的编译脚本

## 关键实现逻辑
1. 从 filamat 容器中提取四类着色器：GLSL、ESSL1、MSL（文本）、SPIR-V（二进制）
2. 使用临时文件传递着色器内容给外部脚本，`ScopedTempFile` RAII 确保清理
3. 移除 MSL 文本着色器时需重建文本字典（LineDictionary）和重新发射 GLSL/ESSL1 chunk
4. SPIR-V chunk 总是重新生成以保证对齐要求
5. 最终计算 CRC32 校验和并写入输出文件

## 依赖关系
- `filaflat/` - chunk 容器读取
- `matdbg/` - 着色器提取
- `filamat/` - chunk 写入和打包
