# Range.h

## 文件概述
表示半开区间 [first, last) 的轻量级范围类，支持迭代和包含检查。

## 核心类/结构体/函数
- **Range<T>**: 范围模板类
  - `size()`, `empty()`: 大小查询
  - `contains(t)`: 包含检查
  - `overlaps(other)`: 重叠检查
  - `begin()` / `end()`: 支持 range-based for 循环
  - **const_iterator**: 随机访问迭代器

## 关键实现逻辑
- last 表示"最后一个之后"，与 STL 惯例一致
- 迭代器直接存储值，解引用返回值的拷贝

## 依赖关系
- 无外部 utils 依赖
