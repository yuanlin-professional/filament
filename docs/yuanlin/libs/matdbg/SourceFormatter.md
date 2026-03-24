# SourceFormatter

## 文件概述

`SourceFormatter` 使用系统安装的 `clang-format` 工具对着色器源码进行格式化，提升 Web 调试界面中着色器代码的可读性。

**源文件**: `src/SourceFormatter.h`, `src/SourceFormatter.cpp`

## 核心类/结构体

### `SourceFormatter`
- **`format(source)`**: 接收着色器源码字符串，返回格式化后的字符串。如果 clang-format 不可用或非 Linux/macOS 平台，则原样返回

## 关键实现逻辑

**Linux/macOS 实现**:
1. 先运行 `clang-format -version` 检查 clang-format 是否可用
2. 将源码写入临时文件 `/tmp/matdbg-tmp-src.cpp`
3. 运行 `clang-format` 并通过管道读取格式化结果
4. 格式化选项：基于 Google 风格、4 空格缩进、最多保留 2 个空行

**其他平台**: 直接返回原始源码。

**一次性警告**: 使用 `std::once_flag` + `std::call_once` 确保 clang-format 不可用的警告只输出一次。

## 依赖关系

- `utils/Log.h` -- 日志输出
