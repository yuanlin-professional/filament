# Lexeme

## 文件概述

`Lexeme` 是一个通用的词法单元模板基类，被 `JsonLexeme` 和 `MaterialLexeme` 继承使用。它记录了词素在源码中的位置信息和文本范围。

**源文件**: `src/Lexeme.h`

## 核心类/结构体

### `Lexeme<T>`
模板参数 `T` 是词素类型枚举（如 `JsonType`、`MaterialType`）。

- **`mStart` / `mEnd`**: 词素文本的起止指针（均为闭区间）
- **`mLineNumber`**: 所在行号
- **`mPosition`**: 行内偏移
- **`mType`**: 词素类型
- **`getSize()`**: 返回 `mEnd - mStart + 1`
- **`getStringValue()`**: 返回 `std::string(mStart, mEnd - mStart + 1)`

## 关键实现逻辑

闭区间设计：`mStart` 和 `mEnd` 均指向有效字符（inclusive），因此大小计算为 `end - start + 1`。

## 依赖关系

- 标准库 `<cstddef>`, `<string>`, `<vector>`
