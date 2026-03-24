# MaterialLexeme

## 文件概述

定义 Filament 材质文件（`.mat`）自定义格式的词法单元类型。材质文件格式是标识符（identifier）和块（block）的交替序列。

**源文件**: `src/MaterialLexeme.h`

## 核心类/结构体

### `MaterialType` 枚举
- `BLOCK` -- 花括号包围的块 `{...}`
- `IDENTIFIER` -- 字母数字组成的标识符（如 `material`、`vertex`、`fragment`）
- `UNKNOWN` -- 未识别的字符

### `MaterialLexeme`
继承自 `Lexeme<MaterialType>`。
- **`trimBlockMarkers()`**: 去除块的首尾花括号，返回新的 MaterialLexeme（start+1, end-1）

## 关键实现逻辑

材质格式非常简单：顶层只有标识符和块两种词素类型。着色器源码（如 GLSL 代码）整体作为一个 BLOCK 词素保留。

## 依赖关系

- `Lexeme.h` -- 模板基类
