# src/EntityManagerImpl.h

## 文件概述
EntityManager 的内部实现类，包含实体创建/销毁的实际逻辑。

## 核心类/结构体/函数
- **EntityManagerImpl**: 继承自 EntityManager
  - `create()`: 从空闲列表或递增索引分配实体
  - `destroy()`: 回收实体索引到空闲列表，增加 generation
  - `registerListener()` / `unregisterListener()`: 管理监听器集合
  - `getEntityCount()`: 计算当前活跃实体数

## 关键实现逻辑
- 使用 deque 作为空闲索引列表，MIN_FREE_INDICES=1024 延迟回收
- 销毁时通知所有注册的监听器
- 监听器集合使用 robin_set，通过 FixedCapacityVector 拷贝后释放锁再回调

## 依赖关系
- `utils/EntityManager.h`, `utils/Mutex.h`, `utils/FixedCapacityVector.h`
