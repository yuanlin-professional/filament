# JsonishLexeme

## 文件概述

定义了 JSON 词法分析的词素（Lexeme）类型。`JsonLexeme` 继承自通用的 `Lexeme<JsonType>` 模板，表示 JSON 流中的一个词法单元。

**源文件**: `src/JsonishLexeme.h`

## 核心类/结构体

### `JsonType` 枚举
JSON 词法单元类型：`BOOLEAN`、`NUll`、`STRING`、`NUMBER`、`BLOCK_START`(`{`)、`BLOCK_END`(`}`)、`COMMA`(`,`)、`ARRAY_START`(`[`)、`ARRAY_END`(`]`)、`COLUMN`(`:`)。

### `JsonLexeme`
- 继承自 `Lexeme<JsonType>`，记录类型、起止指针、行号和位置
- **`trim()`**: 移除 STRING 类型词素两端的双引号字符，返回新的 JsonLexeme
- **`getTypeString(type)`**: 静态方法，将 JsonType 转为可读字符串

## 关键实现逻辑

`trim()` 方法在移除引号时有一个边界保护：如果字符串仅包含单个双引号，trimmed 后 end 会在 start 之前，此时不执行 trim 以避免下溢。

## 依赖关系

- `Lexeme.h` -- 模板基类
