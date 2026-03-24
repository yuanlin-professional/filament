# IDE 配置 (`ide/`)

## 模块概述

`ide/` 目录提供 Filament 项目的 IDE 和编辑器配置文件，确保团队成员使用统一的代码风格和开发环境设置。目前支持 CLion (JetBrains C++ IDE)、Emacs 和 Hex Fiend (macOS 二进制查看器) 三款工具。

## 目录结构

```
ide/
├── clion/                          # CLion / JetBrains IDE 配置
│   ├── C_File_Header.txt           # C 文件头模板 (版权声明)
│   ├── cpp_code_style.xml          # C++ 代码风格配置
│   └── cpp_inspections.xml         # C++ 代码检查规则
├── emacs/                          # Emacs 编辑器配置
│   └── c-filament-style.el         # Filament C 代码风格定义
└── hexfiend/                       # Hex Fiend 二进制查看器
    └── Templates/
        └── Filamesh.tcl            # Filamesh 二进制格式模板
```

## 核心功能

- **CLion 代码风格**: 定义缩进、括号位置、空格规则等 C++ 编码规范，确保团队代码风格一致
- **CLion 代码检查**: 配置静态代码分析规则，帮助开发者在编码时发现潜在问题
- **文件头模板**: 提供标准的 Apache 2.0 版权声明文件头，用于新建文件时自动插入
- **Emacs 风格**: 为 Emacs 用户提供等效的 C/C++ 代码格式化配置
- **二进制格式查看**: Hex Fiend 模板使开发者能以结构化方式查看 Filamesh 二进制文件

## 依赖关系

> 此目录为纯配置文件，不依赖其他模块，也不被任何代码模块依赖。
> 它是供开发者手动导入到各自 IDE 中使用的辅助配置。

| 相关模块 | 说明 |
|---------|------|
| 项目全局 | 代码风格配置适用于整个 Filament 代码库 |
| `tools/filamesh` | Filamesh.tcl 模板对应 filamesh 工具的输出格式 |

## 关键文件说明

| 文件 | 说明 |
|-----|------|
| `clion/cpp_code_style.xml` | CLion/IntelliJ 的 C++ 代码格式化规则，定义缩进宽度、花括号风格、命名约定等 |
| `clion/cpp_inspections.xml` | CLion 静态代码分析的检查规则集合，控制哪些检查启用/禁用及严重级别 |
| `clion/C_File_Header.txt` | 新建 C/C++ 文件时自动插入的 Apache 2.0 开源许可证头部模板 |
| `emacs/c-filament-style.el` | Emacs Lisp 配置，定义 Filament 项目专用的 `c-style`，包含缩进和格式规则 |
| `hexfiend/Templates/Filamesh.tcl` | Hex Fiend 的 Tcl 模板脚本，解析 Filamesh 二进制文件结构并以可读方式展示 |
