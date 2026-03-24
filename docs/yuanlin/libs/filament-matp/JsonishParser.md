# JsonishParser

## 文件概述

JSON 语法解析器，将 `JsonLexeme` 序列解析为 JSON AST（抽象语法树）。采用递归下降法实现 LL(1) 文法。同时定义了完整的 JSON 值类型层次结构。

**源文件**: `src/JsonishParser.h`, `src/JsonishParser.cpp`

## 核心类/结构体

### JSON 值类型层次
- **`JsonishValue`**: 基类，含 Type 枚举（STRING/NUMBER/OBJECT/ARRAY/BOOL/NUll）及类型转换方法
- **`JsonishNull`**: null 值
- **`JsonishBool`**: 布尔值，含 `getBool()`
- **`JsonishNumber`**: 浮点数值，含 `getFloat()`
- **`JsonishString`**: 字符串值，含 `getString()`
- **`JsonishObject`**: 对象，内部用 `unordered_map<string, const JsonishValue*>` 存储键值对
- **`JsonishArray`**: 数组，内部用 `vector<const JsonishValue*>` 存储元素

### `JsonishParser`
- **`parse()`**: 返回解析结果的 `unique_ptr<JsonishObject>`
- **`getParseStatus()`**: 返回解析状态

### 内部解析方法
- `parseObject()`, `parseArray()`, `parsePair()`, `parseValue()`, `parseMembers()`, `parseElements()`, `parseString()`

## 关键实现逻辑

**LL(1) 递归下降**: 每个产生式对应一个方法。`parseValue()` 根据下一个词素类型分发到对应的解析方法。

**转义序列处理**: `resolveEscapes()` 函数处理 JSON 字符串中的 `\\`、`\/`、`\t`、`\r`、`\n` 等转义序列。暂不支持 `\u` Unicode 转义。

**宽松语法**: 字符串可以携带数组后缀（如 `"float[9]"`），`parseString()` 会尝试解析后面的数组部分并将其合并到字符串值中。

**末尾逗号**: `parseMembers()` 允许最后一个键值对后面出现逗号。

## 依赖关系

- `JsonishLexeme.h` -- JsonLexeme 输入
- `utils/Status.h` -- 错误状态报告
