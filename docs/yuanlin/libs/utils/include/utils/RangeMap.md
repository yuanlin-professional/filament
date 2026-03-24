# RangeMap.h

## 文件概述
稀疏区间映射容器，存储有序的非重叠区间，每个区间关联一个值。

## 核心类/结构体/函数
- **RangeMap<KeyType, ValueType>**: 区间映射类
  - `add(first, last, value)`: 添加或替换区间
  - 自动进行区间的分割和合并

## 关键实现逻辑
- 内部使用 std::map 按区间起始键排序
- 添加新区间时自动拆分重叠区间并合并相邻同值区间
- 适合存储较均匀数据时的低内存开销

## 依赖关系
- `utils/Panic.h`, `utils/Range.h`
