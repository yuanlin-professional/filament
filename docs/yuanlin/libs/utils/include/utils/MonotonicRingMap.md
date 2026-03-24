# MonotonicRingMap.h

## 文件概述
固定容量的环形映射表，键单调递增，满时覆盖最旧元素，不进行堆分配。

## 核心类/结构体/函数
- **MonotonicRingMap<N, KEY, VALUE>**: 环形映射模板
  - 固定容量 N，使用 std::array 存储
  - 键必须单调递增
  - 查找复杂度 O(log N)

## 关键实现逻辑
- 利用键单调递增的特性，使用环形缓冲区覆盖旧数据
- 查找使用二分搜索

## 依赖关系
- `utils/compiler.h`
