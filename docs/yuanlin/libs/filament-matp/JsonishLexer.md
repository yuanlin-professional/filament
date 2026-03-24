# JsonishLexer

## 文件概述

JSON 词法分析器，将字符流分解为 `JsonLexeme` 序列。支持宽松的 JSON 语法：无引号的标识符被视为字符串，注释（`//` 风格）会被跳过。

**源文件**: `src/JsonishLexer.h`, `src/JsonishLexer.cpp`

## 核心类/结构体

### `JsonishLexer`
继承自 `Lexer<JsonLexeme>`，实现 `readLexeme()` 虚方法。

- **`readLexeme()`**: 跳过空白后根据当前字符判断词素类型：`"` 开头为字符串，数字或 `-` 开头为数值，标点符号为对应类型，标识符字符则作为字符串处理
- **`readIdentifier()`**: 读取字母数字和下划线组成的标识符，识别 `true`/`false`/`null` 关键字
- **`readString()`**: 读取双引号包围的字符串内容
- **`readNumber()`**: 读取整数和小数部分（支持负号和小数点）
- **`readPunctuation()`**: 读取单字符标点（`,` `:` `[` `]` `{` `}`），未知字符作为标识符处理

## 关键实现逻辑

词法分析后立即对每个词素调用 `trim()` 去除字符串两端的引号。Lexer 基类提供了统一的空白跳过和行号追踪机制。

## 依赖关系

- `Lexer.h` -- 模板基类，提供 `lex()`、`consume()`、`peek()` 等基础设施
- `JsonishLexeme.h` -- JsonLexeme 和 JsonType 定义
