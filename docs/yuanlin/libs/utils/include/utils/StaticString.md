# StaticString.h

## 文件概述
轻量级静态字符串类，仅存储字符串字面量的指针和长度，不进行任何内存分配。

## 核心类/结构体/函数
- **StaticString**: 静态字符串类
  - 从字符串字面量构造（编译期确定长度）
  - 提供只读的 string_view 兼容接口
  - `c_str()`, `data()`, `size()`, `empty()`
  - 支持比较运算符

## 关键实现逻辑
- 内部使用 std::string_view 存储
- 模板构造函数自动推导字符串字面量长度
- 零内存分配，适合频繁使用的固定字符串

## 依赖关系
- `utils/compiler.h`, `utils/ostream.h`
