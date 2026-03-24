# database/database.go

## 文件概述
beamsplitter 的类型定义数据库模块。负责将 AST 转换为扁平化的类型定义列表（包含结构体和枚举），并为结构体字段添加类型限定符。

## 核心类/接口
- `TypeDefinition` - 类型定义接口，提供名称和父级查询方法
- `StructDefinition` - 结构体定义，包含字段列表和描述
- `StructField` - 结构体字段，包含类型、默认值、文档和 emitter 标志
- `EnumDefinition` - 枚举定义，包含枚举值列表
- `gatherContext` - 收集上下文，持有正则表达式和遍历状态

## 关键实现逻辑
1. `Create()` 函数为入口，编译正则表达式、收集注释块、对 AST 进行前序遍历
2. `gatherTypeDefinitions()` 递归遍历 AST 节点（命名空间、枚举、结构体、类），构建扁平的类型定义列表
3. `addTypeQualifiers()` 在命名空间层次中向上搜索，为结构体字段关联自定义类型（枚举/结构体），并为枚举字段的默认值添加完全限定名
4. `distillValue()` 转换 C++ 值字面量（去除浮点后缀 `f`，将花括号向量转为方括号）
5. `gatherCommentBlocks()` 使用逐行扫描提取 `/** ... */` 块注释，映射到行号

## 依赖关系
- `beamsplitter/parse` - 提供 AST 节点类型
