# parse/parse.go

## 文件概述
beamsplitter 的语法分析器（Parser）。从词法分析器的 token 流中构建完整的 AST。

## 核心函数
- `Parse()` - 公开入口，创建词法分析器并启动解析，返回 `RootNode`
- `parseRoot()` / `parseNamespace()` - 解析顶层命名空间
- `parseStruct()` / `parseClass()` - 解析结构体和类定义
- `parseEnum()` - 解析枚举定义
- `parseUsing()` - 解析类型别名
- `parseStructBody()` - 解析结构体/类的内部成员（字段、方法、嵌套类型、访问修饰符）

## 关键实现逻辑
1. 采用递归下降解析策略，消耗词法单元并递归构建 AST 节点
2. `parseStructBody()` 处理最复杂的逻辑：区分字段声明（带可选默认值和数组长度）和方法声明
3. 前向声明（仅有分号的 struct/class）返回 nil 并被过滤
4. `expect()` 函数断言下一个 token 的类型，不匹配时调用 `panic()` 终止并打印错误行号

## 依赖关系
- 内部使用 `lexer` 类型及其 token 流
