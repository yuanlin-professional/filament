# resgen - 二进制资源聚合与嵌入代码生成器

## 模块名称和概述

`resgen` 是 Filament 的资源聚合工具，将一组二进制文件（如编译后的材质、纹理数据等）打包为单个资源包，并生成可直接嵌入 C/C++ 项目的头文件和汇编文件。它是 Filament 构建管线中将离线资产嵌入到引擎二进制中的关键步骤。

## 目录结构

```
tools/resgen/
    CMakeLists.txt      # CMake 构建配置
    src/
        main.cpp        # 主程序源码，包含资源聚合和代码生成逻辑
```

## 核心功能

- **资源聚合**: 将多个二进制文件合并为单个 `.bin` 数据块
- **C/C++ 头文件生成**: 生成 `resources.h`，声明每个资源的大小和偏移量
- **汇编文件生成**: 生成 `.S` 汇编文件（Linux/Android）和 `.apple.S`（macOS/iOS），使用 `incbin` 指令嵌入二进制数据
- **Zstd 压缩**: 支持通过 `--compress` 参数对资源进行 Zstd 压缩
- **JSON 元数据**: 可通过 `--json` 嵌入 JSON 格式的资源清单信息
- **空字符追加**: `--text` 模式在每个资源末尾追加空字符，便于作为字符串使用
- **符号命名**: 默认使用文件基名作为符号名，`--keep` 保留文件扩展名

### 使用方式

```bash
# 基本用法
resgen -p my_resources -x output_dir material1.filamat material2.filamat

# 使用压缩
resgen --compress -p shaders -x output_dir shader1.bin shader2.bin

# 生成 C 代码（而非汇编）
resgen --c -p resources -x output_dir data.bin
```

### 输出文件

- `<package>.h` - C/C++ 头文件，声明资源大小和偏移量
- `<package>.S` - Linux/Android 汇编文件
- `<package>.apple.S` - macOS/iOS 汇编文件
- `<package>.bin` - 聚合后的二进制数据块

## 依赖关系

- **utils** - Filament 通用工具库（路径处理、命令行解析）
- **zstd** - Facebook 的 Zstandard 压缩库

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `src/main.cpp` | 完整的工具实现，包含命令行解析、资源聚合、头文件和汇编代码生成 |
| `CMakeLists.txt` | 构建配置，链接 utils 和 zstd 库 |
