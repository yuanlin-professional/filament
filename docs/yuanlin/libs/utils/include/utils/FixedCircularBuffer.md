# FixedCircularBuffer.h

## 文件概述
固定容量的环形缓冲区，满时自动覆盖最旧元素。

## 核心类/结构体/函数
- **FixedCircularBuffer<T>**: 环形缓冲区模板类
  - `push(v)`: 压入元素，满时返回被覆盖的旧值
  - `pop()`: 弹出最旧的元素
  - `size()` / `capacity()` / `full()` / `empty()`: 状态查询

## 关键实现逻辑
- 使用 begin/end 双指针和取模运算实现环形访问
- push 在缓冲区满时先 pop 再写入，返回被替换的元素

## 依赖关系
- 无外部 utils 依赖
