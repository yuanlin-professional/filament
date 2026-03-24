# main.go

## 文件概述
beamsplitter 工具的入口文件。该工具是一个 Go 语言编写的代码生成器，用于解析 Filament 引擎的 `Options.h` 头文件，自动为 C++ 序列化器、JavaScript/TypeScript 绑定以及 Java 绑定生成对应的代码。

## 核心函数
- `findFilamentRoot()` - 根据当前文件路径自动定位 Filament 项目根目录
- `main()` - 入口函数，依次执行解析 -> 构建数据库 -> 生成各平台代码

## 关键实现逻辑
1. 读取 `filament/include/filament/Options.h` 文件内容
2. 调用 `parse.Parse()` 生成 AST（抽象语法树）
3. 调用 `db.Create()` 从 AST 构建类型定义数据库
4. 依次调用各 emitter 生成 C++ 序列化代码、JavaScript 绑定、TypeScript 类型声明和 Java 绑定
5. 可选地通过 `CHECK_API_SYNTAX` 标志检查所有 Filament API 头文件的语法

## 依赖关系
- `beamsplitter/database` - 类型定义数据库
- `beamsplitter/emitters` - 各平台代码生成器
- `beamsplitter/parse` - C++ 头文件解析器
