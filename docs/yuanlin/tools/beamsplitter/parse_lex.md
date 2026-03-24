# parse/lex.go

## 文件概述
beamsplitter 的词法分析器（Lexer）。采用 goroutine 并发模型，将 C++ 头文件内容拆分为 token 流，通过 channel 传递给解析器。

## 核心类型
- `item` - 词法单元，包含类型、位置、值和行号
- `itemType` - 词法单元类型枚举（标识符、关键字、分隔符等）
- `lexer` - 词法分析器状态机，包含输入字符串、光标位置和 lookahead 缓冲

## 关键实现逻辑
1. `createLexer()` 创建词法分析器并启动 goroutine 进行并发扫描
2. 采用递归下降法，每个非终结符对应一个 `lex*` 函数（`lexRoot`、`lexNamespace`、`lexStruct` 等）
3. `lookahead()` 机制用于尝试性解析：保存状态，缓冲所有发射的 token，失败时回滚
4. `discardOptionalSpace()` 跳过空白、注释（`//` 和 `/* */`）以及预处理器指令
5. `lexSimpleType()` 解析简化的 C++ 类型表达式（包含 `const`、指针、引用、模板参数）
6. 支持识别的关键字包括：`namespace`、`struct`、`class`、`enum`、`using`、`template`、`const`、`friend` 等

## 依赖关系
- 无外部包依赖
