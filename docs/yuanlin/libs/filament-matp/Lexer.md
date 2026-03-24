# Lexer

## 文件概述

`Lexer` 是一个通用的词法分析器模板基类，提供字符流遍历、空白和注释跳过、行号追踪等基础设施。`JsonishLexer` 和 `MaterialLexer` 均继承此类。

**源文件**: `src/Lexer.h`

## 核心类/结构体

### `Lexer<T>`
模板参数 `T` 是词素类型（如 `JsonLexeme`、`MaterialLexeme`）。

- **`lex(text, size, lineOffset)`**: 入口方法，设置游标后循环调用 `readLexeme()` 直到数据读完
- **`getLexemes()`**: 返回解析出的词素向量
- **`readLexeme()`**: 纯虚方法，子类实现具体的词素读取逻辑
- **`consume()`**: 消费当前字符并推进游标，遇到换行时更新行号计数
- **`peek()`**: 查看当前字符
- **`skipWhiteSpace()`**: 跳过空白字符和 `//` 风格的单行注释
- **字符分类方法**: `isWhiteSpaceCharacter`、`isAlphaCharacter`、`isNumericCharacter`、`isIdentifierCharacter` 等

## 关键实现逻辑

空白字符判定采用 ASCII 码范围（`c < 33 || c > 127`），这意味着所有控制字符和非 ASCII 字符都被视为空白。`skipWhiteSpace` 在跳过空白后还会检查是否是 `//` 注释，若是则继续跳到行尾后递归跳过。

## 依赖关系

- `Lexeme.h` -- 词素基类
