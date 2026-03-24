# sstream.h / sstream.cpp

## 文件概述
字符串输出流，继承自 io::ostream，将输出收集到内部缓冲区。

## 核心类/结构体/函数
- **io::sstream**: 字符串流类
  - `c_str()`: 获取缓冲区内容的 C 字符串
  - `length()`: 获取缓冲区长度
  - `flush()`: 重写的刷新方法（无操作）

## 关键实现逻辑
- 继承自 io::ostream，重写 flush() 为空操作
- 所有写入的数据保留在内部 Buffer 中

## 依赖关系
- `utils/compiler.h`, `utils/ostream.h`
