# RefCountedMap.h

## 文件概述
引用计数映射容器，结合 LRU 缓存，支持自动管理资源的生命周期。

## 核心类/结构体/函数
- **RefCountedMap<Key, Value>**: 引用计数映射
  - 当引用计数归零时，元素可移入 LRU 缓存而非立即删除
  - 支持自定义淘汰回调

## 关键实现逻辑
- 基于 robin_map 实现快速查找
- 集成 LruCache 延迟回收不再使用的资源
- 适合管理 GPU 资源等创建/销毁成本高的对象

## 依赖关系
- `utils/Panic.h`, `utils/compiler.h`, `utils/debug.h`, `utils/LruCache.h`
