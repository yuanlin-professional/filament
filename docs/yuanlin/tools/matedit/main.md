# main.cpp (matedit)

## 文件概述
MATEDIT 命令行工具入口。允许对 matc 编译生成的材质文件进行编辑操作，当前支持 `external-compile` 命令，用于将文本着色器编译为预编译二进制。

## 核心函数
- `main()` - 解析参数 -> 解析命令 -> 收集脚本参数 -> 调用 `externalCompile()`

## 关键实现逻辑
1. 命令行结构：`matedit [options] -i input -o output external-compile -- script [args]`
2. `--` 分隔符之后的参数作为外部脚本的路径和参数
3. `--preserve-text-shaders` 选项控制是否在输出中保留原始文本着色器

## 依赖关系
- `ExternalCompile` - 外部编译核心逻辑
