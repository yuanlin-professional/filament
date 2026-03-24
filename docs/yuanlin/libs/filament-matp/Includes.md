# Includes

## 文件概述

实现材质文件中 `#include` 指令的递归解析和展开。支持在展开前后插入 `#line` 指令以保持正确的行号信息，便于调试和错误报告。

**源文件**: `src/Includes.h`, `src/Includes.cpp`

## 核心类/结构体

### `ResolveOptions`
- `insertLineDirectives` -- 是否在 include 展开处插入 `#line` 指令
- `insertLineDirectiveCheck` -- 是否用 `#if defined(GL_GOOGLE_cpp_style_line_directive)` 包裹 `#line` 指令
- `resolveIncludes` -- 是否处理 `#include` 指令

### `FoundInclude`
- 描述解析出的一个 `#include` 指令：文件名、在源码中的起始位置、长度、行号

### 关键函数
- **`resolveIncludesRecursively(root, callback, options, depth)`**: 递归展开 root 中的所有 include，深度限制 30 层防止循环引用
- **`parseForIncludes(source)`**: 在源码中查找所有 `#include "..."` 指令，自动忽略注释中的 include

## 关键实现逻辑

**注释排除**: `findCommentRanges` 函数分别查找 `/* */` 和 `//\n` 两种注释区间，`parseForIncludes` 在找到 `#include` 后检查其是否位于注释区间内。

**`#line` 指令插入**: 从后往前处理每个 include（避免偏移失效），在 include 前插入 `#line 1 "included_file.h"`，在后插入 `#line N "root.h"` 恢复原始行号。

**递归展开**: 每次处理列表中第一个 include，通过 callback 获取内容后递归调用自身处理嵌套 include，然后用展开结果替换原始 `#include` 文本。

## 依赖关系

- `IncludeCallback.h` -- IncludeResult、IncludeCallback
- `utils/CString.h` -- CString 及其 insert/replace 操作
- `utils/Log.h`, `utils/sstream.h` -- 日志和字符串流
