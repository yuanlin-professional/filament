# CircularBuffer.h

## 文件概述

定义了 `CircularBuffer` 类，实现了一个高效的环形缓冲区。这是 Filament 命令流系统的底层存储机制，用于在渲染线程和驱动线程之间传递命令数据。

## 核心类

### CircularBuffer

环形缓冲区实现，不可复制或移动。

**设计建议**:
- 缓冲区大小至少为 2 倍所需大小（避免 flush 阻塞）
- 建议设为 3 倍所需大小（避免阻塞渲染/UI 线程）

**内部类型**:
- `Range` - 缓冲区范围（tail/head 指针对）

## 主要 API

| 方法 | 说明 |
|------|------|
| `CircularBuffer(bufferSize)` | 构造函数，指定总缓冲区大小 |
| `size()` | 返回缓冲区总大小（常量） |
| `allocate(s)` | 分配 `s` 字节并返回指针，总分配不能超过 `size()` |
| `empty()` | 自上次 `getBuffer()` 以来是否有分配 |
| `getUsed()` | 自上次 `getBuffer()` 以来已使用的字节数 |
| `getBuffer()` | 获取当前已分配范围并释放，调用者负责确保返回范围不再使用 |
| `getBlockSize()` | 静态方法，返回系统页大小 |

## 依赖关系

- `utils/debug.h` - 断言宏
