# MaterialLexer

## 文件概述

Filament 材质文件格式的词法分析器，将 `.mat` 文件内容分解为 `MaterialLexeme` 序列（IDENTIFIER 和 BLOCK 交替）。

**源文件**: `src/MaterialLexer.h`, `src/MaterialLexer.cpp`

## 核心类/结构体

### `MaterialLexer`
继承自 `Lexer<MaterialLexeme>`。

- **`readLexeme()`**: 跳过空白后调用 `peek()` 判断下一个词素类型，然后分发到对应方法
- **`peek(type)`**: 根据当前字符判断类型：字母 -> IDENTIFIER，`{` -> BLOCK，其他 -> UNKNOWN
- **`readBlock()`**: 使用花括号计数器追踪嵌套层级，消费从 `{` 到对应 `}` 的所有字符。内部会跳过空白，块格式不正确时返回 false
- **`readIdentifier()`**: 消费连续的字母数字字符
- **`readUnknown()`**: 消费单个未识别字符

## 关键实现逻辑

`readBlock()` 使用 `braceCount` 计数嵌套的花括号。遇到 `{` 时加一，遇到 `}` 时减一，当计数归零时块结束。这保证了嵌套块（如着色器代码中的 `if {} else {}`）能被正确处理。

如果遇到 UNKNOWN 类型的字符，`readLexeme()` 返回 false 中止词法分析，这用于检测输入是否为二进制文件。

## 依赖关系

- `Lexer.h` -- 模板基类
- `MaterialLexeme.h` -- MaterialLexeme 和 MaterialType
