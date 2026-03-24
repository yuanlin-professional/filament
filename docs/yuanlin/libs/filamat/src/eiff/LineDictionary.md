# LineDictionary.h / LineDictionary.cpp

## 文件概述

`LineDictionary` 实现了一个行级文本字典，用于对着色器源代码进行基于行的去重压缩。它将文本按行拆分并为每一行分配唯一索引，同时支持按模式（如 `_123`、`hp_copy_456`）进一步拆分子行，以提高压缩率。

## 核心类

### `LineDictionary`

- **`addText(string_view text)`**: 将整段文本按换行符分割成行并逐行添加到字典。
- **`getString(index_t index)`**: 根据索引获取对应的字符串。
- **`getIndices(string_view line)`**: 将一行文本拆分为子行，返回每个子行在字典中的索引列表。若任一子行未找到则返回空。
- **`getDictionaryLineCount()` / `size()`**: 返回唯一行总数。
- **`printStatistics(ostream&)`**: 输出字典压缩统计信息（压缩率、平均行长等）。

### 内部结构

- **`LineInfo`**: 记录行的 `index`（字典索引）和 `count`（出现次数）。
- **`splitString(string_view)`**: 静态方法，通过 `findPattern()` 将行拆分为多个子字符串。模式匹配规则：在单词边界处查找 `hp_copy_`、`mp_copy_` 或 `_` 前缀后跟数字的模式。
- **`findPattern(string_view, offset)`**: 查找下一个匹配模式的位置和长度。
- **`ltrim(string_view)`**: 去除前导空白。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mLineIndices` | `unordered_map<string_view, LineInfo>` | 行文本到索引和计数的映射 |
| `mStrings` | `vector<unique_ptr<string>>` | 拥有所有唯一行字符串的存储 |

## 关键实现逻辑

核心压缩策略是将着色器代码按行去重。为了进一步提高压缩率，`splitString` 会识别变量名中的数字后缀模式（如 `_0`、`hp_copy_1`），将行拆分为不含数字后缀的通用部分和数字后缀部分，使得不同变量编号的相同代码模式能共享字典条目。统计数据显示压缩率可达约 2.8x~12.5x。

## 依赖关系

- `<utils/Log.h>` / `<utils/debug.h>` -- 日志与断言
- `<unordered_map>` / `<vector>` / `<string_view>` / `<memory>` -- 标准库
