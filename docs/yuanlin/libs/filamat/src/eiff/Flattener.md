# Flattener.h

## 文件概述

`Flattener` 是 EIFF 二进制序列化的核心写入器，负责将各种基本类型（bool、uint8/16/32/64、字符串、二进制块）按小端字节序写入到内存缓冲区。同时支持"DryRunner"模式，仅计算字节偏移而不实际写入数据。

## 核心类

### `Flattener`

- **构造函数**: `explicit Flattener(uint8_t* dst)` -- 指定写入目标缓冲区。
- **`getDryRunner()`**: 静态方法，返回一个不实际写入数据的 Flattener 实例，用于预计算序列化大小。使用伪地址 `0x1` 避免 UBSan 空指针偏移错误。
- **基本写入方法**: `writeBool`、`writeUint8`、`writeUint16`、`writeUint32`、`writeUint64` -- 小端字节序写入。
- **字符串写入**: `writeString(const char*)` / `writeString(string_view)` -- 写入以 null 结尾的字符串。
- **二进制写入**: `writeBlob(const char*, size_t)` -- 先写入 uint64 长度再写入数据；`writeRaw` -- 直接写入原始字节。
- **占位符机制**:
  - `writeSizePlaceholder()` / `writeSize()` -- 先写 4 字节占位，后续回填实际大小。
  - `writeOffsetPlaceholder(index)` / `writeOffsets(index)` -- 偏移量占位与回填，支持多对一映射。
  - `writeValuePlaceholder()` / `writeValue(v)` -- 通用值占位与回填。
- **`writeAlignmentPadding()`**: 写入 0-7 个零字节使光标对齐到 8 字节边界。
- **`getBytesWritten()`**: 返回已写入的总字节数。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCursor` | `uint8_t*` | 当前写入位置 |
| `mStart` | `uint8_t*` | 缓冲区起始地址 |
| `mSizePlaceholders` | `vector<uint8_t*>` | 大小占位符栈 |
| `mOffsetPlaceholders` | `multimap<size_t, uint8_t*>` | 偏移占位符映射 |
| `mValuePlaceholders` | `vector<uint8_t*>` | 值占位符栈 |
| `mOffsetsBase` | `uint8_t*` | 偏移计算的基准地址 |

## 关键实现逻辑

所有写入方法在 DryRunner 模式下仅移动光标而不写入数据，从而实现零成本的大小预计算。占位符机制通过栈/映射记录需要回填的位置，支持先写占位再回填实际值的两阶段写入模式。

## 依赖关系

- `<utils/Log.h>` / `<utils/debug.h>` -- 日志与断言
- `<map>` / `<vector>` / `<string_view>` -- 标准库容器
- `<stdint.h>` / `<string.h>` / `<stdlib.h>` -- C 标准库
