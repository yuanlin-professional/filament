# IncludeCallback

## 文件概述

定义了材质文件 `#include` 解析系统的核心数据结构 `IncludeResult` 和回调类型 `IncludeCallback`。这是 include 解析框架的接口层，具体实现由 `DirIncluder` 等类提供。

**源文件**: `src/IncludeCallback.h`

## 核心类/结构体

### `IncludeResult`
- **`includeName`**: include 文件名（如 `#include "foobar.h"` 中的 `foobar.h`），仅在启用 `#line` 指令时使用
- **`text`**: 被包含文件的完整内容，可能包含递归的 `#include` 指令
- **`name`**: 文件的标识名称。对于 `DirIncluder`，根文件为空字符串，后续文件为规范路径

### `IncludeCallback`
- 类型: `std::function<bool(const CString& includedBy, IncludeResult& result)>`
- 当发现 `#include "file.h"` 时被调用，`includedBy` 是包含方文件的名称，`result` 中的 `includeName` 已填写被包含文件名，回调需要填充 `text` 和 `name` 字段

## 关键实现逻辑

纯接口定义文件，无实现逻辑。设计上将 include 解析的策略（如何查找文件、如何读取内容）与机制（递归展开 include）分离。

## 依赖关系

- `utils/CString.h` -- CString 字符串类
- `utils/Status.h` -- 状态返回类型
