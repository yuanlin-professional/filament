# beamsplitter - C++ 头文件解析与多语言绑定代码生成器

## 模块名称和概述

`beamsplitter` 是一个使用 Go 语言编写的代码生成工具。它解析 Filament 的 C++ 头文件（主要是 `Options.h`），并自动生成 Java 绑定、JavaScript/TypeScript 绑定以及 C++ JSON 序列化代码。该工具简化了多平台 API 同步维护的工作量。

## 目录结构

```
tools/beamsplitter/
    main.go              # 程序入口，定义源文件路径和输出目标
    go.mod               # Go 模块定义文件
    README.md            # 原始英文文档
    database/
        database.go      # AST 数据库构建，将解析结果转化为结构化定义
    emitters/
        serializer.go    # C++ JSON 序列化代码生成器
        serializer.template  # 序列化代码模板
        javascript.go    # JavaScript/TypeScript 绑定生成器
        javascript.template  # JavaScript 代码模板
        java.go          # Java 绑定生成器
        java.template    # Java 代码模板
    parse/
        lex.go           # 词法分析器（lexer）
        parse.go         # 语法解析器（parser），实现递归下降解析
        ast.go           # 抽象语法树（AST）节点定义
```

## 核心功能

- **C++ 头文件解析**: 使用递归下降解析器读取 C++ 头文件，构建粗粒度 AST
- **Java 绑定生成**: 自动更新 Android 端 `View.java` 中的相关字段
- **JavaScript 绑定生成**: 生成 `filament-js` 的绑定代码和 TypeScript 类型声明
- **C++ 序列化生成**: 生成 `Settings_generated.h/cpp` 用于 JSON 序列化支持
- **发射器标记**: 通过 `%codegen_skip_json%` 等注释标记控制特定字段的生成行为

### 使用方式

```bash
# 安装 Go 编译器（macOS）
brew install go

# 运行代码生成器
cd tools/beamsplitter && go run .
```

### 输出文件

- `libs/viewer/src/Settings_generated.h` / `.cpp` - C++ 序列化代码
- `web/filament-js/jsbindings_generated.cpp` - JavaScript 绑定
- `web/filament-js/jsenums_generated.cpp` - JavaScript 枚举定义
- `web/filament-js/extensions_generated.js` - JavaScript 扩展
- `web/filament-js/filament.d.ts` - TypeScript 类型声明（原地编辑）
- `android/filament-android/.../View.java` - Java 类（原地编辑）

## 依赖关系

- **运行时依赖**: Go 编译器（1.18+）
- **输入文件**: `filament/include/filament/Options.h`
- **无外部 Go 依赖**: 仅使用标准库

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `main.go` | 程序主入口，协调解析和代码生成流程 |
| `parse/lex.go` | 词法分析器，将 C++ 源码分割为 token 流，处理预处理指令和注释 |
| `parse/parse.go` | 递归下降语法解析器，从 token 流构建 AST |
| `parse/ast.go` | AST 节点类型定义（namespace、struct、enum、field 等） |
| `database/database.go` | 将 AST 转化为可供发射器使用的结构化数据库 |
| `emitters/serializer.go` | 基于模板生成 C++ JSON 序列化代码 |
| `emitters/javascript.go` | 生成 JavaScript 绑定和 TypeScript 类型声明 |
| `emitters/java.go` | 生成并更新 Java 绑定代码 |
