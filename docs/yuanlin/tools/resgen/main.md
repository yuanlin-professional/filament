# main.cpp (resgen)

## 文件概述
RESGEN 资源聚合器。将多个二进制文件打包为单一资源包，生成 C 头文件（声明偏移和大小宏）、汇编文件（`.S`，用 `.incbin` 嵌入二进制）和可选的 C 源文件（xxd 风格）。支持 zstd 压缩。

## 核心函数
- `main()` - 入口：解析参数 -> 逐文件读取 -> 聚合 -> 输出头文件/汇编文件/二进制包
- `writeHeaderIfChanged()` - 仅在内容变化时写入头文件（避免不必要的重编译）
- `writeXxdEntry()` - 将资源数据写为 C 十六进制数组

## 关键实现逻辑
1. 资源名称由文件名转换为大写符号，加上包名前缀（如 `RESOURCES_JUNGLE_DATA`）
2. 生成两种汇编文件：标准 ELF（`.rodata` section）和 Apple（`__TEXT,__const` section，带下划线前缀）
3. 支持单资源独立压缩（`-z`）和整包压缩（`-Z`），两者互斥
4. `--json` 模式在包末尾嵌入资源元数据 JSON，包含大小信息
5. `--text` 模式在每个资源末尾追加 null 终止符

## 依赖关系
- `zstd` - Zstandard 压缩库
- `utils/Path` - 路径工具
