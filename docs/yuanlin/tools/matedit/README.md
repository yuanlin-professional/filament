# matedit - 已编译材质文件编辑工具

## 模块名称和概述

`matedit` 是一个用于编辑已编译 Filament 材质文件（`.filamat`/`.cmat`）的命令行工具。其主要功能是对已编译材质中的文本着色器执行外部编译操作，例如将 Metal 着色器文本编译为预编译的 Metal 库（`.metallib`），从而减少运行时的着色器编译开销。

## 目录结构

```
tools/matedit/
    CMakeLists.txt      # CMake 构建配置
    src/
        main.cpp        # 命令行入口和参数处理
        ExternalCompile.h    # 外部编译接口定义
        ExternalCompile.cpp  # 外部编译核心实现
```

## 核心功能

- **外部着色器编译**: 通过用户提供的脚本将材质中的文本着色器编译为二进制格式
- **Metal 支持**: 目前专注支持 Metal 着色器的预编译（text -> metallib）
- **并行编译**: 多个着色器编译任务可并行执行以提高效率
- **文本着色器保留**: 可选保留原始文本着色器（`--preserve-text-shaders`）
- **输入/输出分离**: 明确区分输入和输出文件路径

### 使用方式

```bash
# 基本用法：使用外部脚本编译 Metal 着色器
matedit -o out.cmat -i in.cmat --type metal external-compile -- ./compile-script.sh

# 保留文本着色器
matedit -o out.cmat -i in.cmat --type metal -p external-compile -- ./compile-script.sh --sdk iphoneos

# 外部脚本接收的参数：
#   $1 = 输入着色器临时文件路径（.metal）
#   $2 = 输出二进制临时文件路径（.metallib）
#   $3 = 着色器阶段（vertex/fragment/compute）
#   $4 = 目标平台（desktop/mobile）
#   $5+ = 用户自定义参数
```

## 依赖关系

- **matdbg** - Filament 材质调试库，提供材质文件的读取和修改能力
- **filamat**（间接）- 材质数据结构定义

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 命令行入口，解析 `--input`/`--output`/`--type` 等参数 |
| `src/ExternalCompile.h/cpp` | 核心逻辑：遍历材质中的文本着色器，调用外部脚本编译，将二进制结果写回 |
| `CMakeLists.txt` | 构建配置，链接 matdbg 库 |
