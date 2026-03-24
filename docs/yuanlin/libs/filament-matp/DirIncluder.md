# DirIncluder

## 文件概述

`DirIncluder` 是一个函数对象（Functor），实现了 `IncludeCallback` 的语义，用于解析材质文件中 `#include` 指令时从文件系统加载被包含文件的内容。

**源文件**: `src/DirIncluder.h`, `src/DirIncluder.cpp`

## 核心类/结构体

### `DirIncluder`
- **`setIncludeDirectory(Path dir)`**: 设置根 include 搜索目录
- **`operator()(includedBy, result)`**: 回调入口。根据 `includedBy` 参数决定搜索路径：若为空则从根目录搜索，否则从包含方文件所在目录进行相对路径搜索。读取文件内容填充到 `result.text` 和 `result.name`

## 关键实现逻辑

搜索路径策略：
1. 若 `includedBy` 为空字符串（表示根材质文件），则在 `mIncludeDirectory` 下查找
2. 否则，以 `includedBy` 所在目录为基准，拼接 `result.includeName` 进行相对路径查找

找到文件后，先验证路径是否为文件（`isFile`），然后用 `std::ifstream` 以二进制模式读取全部内容。

## 依赖关系

- `IncludeCallback.h` -- IncludeResult 结构体定义
- `utils/Path.h` -- 文件路径操作
- `utils/Log.h` -- 日志输出
