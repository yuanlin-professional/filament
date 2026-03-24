# InternPool.h

## 文件概述
引用计数的驻留池 (intern pool)，用于去重和共享相同内容的 Slice 数据。

## 核心类/结构体/函数
- **InternPool<T, Hash>**: 驻留池模板类
  - `acquire(slice)`: 获取驻留副本，已存在则增加引用计数
  - `release(slice)`: 释放引用，引用计数归零时删除
  - `hash()`: 计算 Slice 的哈希值
  - `empty()`: 检查池是否为空

## 关键实现逻辑
- 使用 robin_map 存储 Slice -> (引用计数, 数据) 的映射
- acquire 时自动合并相同内容，返回共享的 Slice 视图
- release 时引用计数归零自动清理

## 依赖关系
- `utils/Slice.h`, `utils/FixedCapacityVector.h`, `utils/Hash.h`, `utils/Panic.h`, `utils/debug.h`
