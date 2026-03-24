# LruCache.h

## 文件概述
基于 robin_map 的 LRU (最近最少使用) 缓存实现。

## 核心类/结构体/函数
- **LruCache<Key, Value, Hash, Equal>**: LRU 缓存模板类
  - 通过双向链表维护访问顺序
  - 容量满时自动淘汰最久未访问的元素
  - 支持自定义哈希和相等比较函数

## 关键实现逻辑
- 使用 robin_map 实现 O(1) 查找
- 双向链表头部为最近使用，尾部为最久未使用
- 每次访问将元素移到链表头部

## 依赖关系
- `utils/Allocator.h`, `utils/compiler.h`
