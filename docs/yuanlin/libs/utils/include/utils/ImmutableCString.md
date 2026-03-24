# ImmutableCString.h / ImmutableCString.cpp

## 文件概述
不可变 C 字符串类，区分静态字符串字面量和堆分配字符串，避免不必要的内存分配。

## 核心类/结构体/函数
- **ImmutableCString**: 不可变字符串类（<= 16 字节）
  - 字符串字面量构造时不分配内存（static 模式）
  - 动态字符串构造时进行堆分配（dynamic 模式）
  - `isStatic()` / `isDynamic()`: 查询存储模式
  - 只读接口：`c_str()`, `data()`, `size()`, `begin()`, `end()`

## 关键实现逻辑
- 模板构造函数区分字符串字面量（编译期）和动态字符串
- 静态模式仅保存指针，拷贝零成本；动态模式使用 malloc 分配
- 可选的分配跟踪统计，区分 static/heap 分配计数

## 依赖关系
- `utils/compiler.h`, `utils/StaticString.h`, `utils/ostream.h`, `utils/Logger.h`
