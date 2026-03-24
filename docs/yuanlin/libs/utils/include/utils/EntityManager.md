# EntityManager.h / EntityManager.cpp / EntityManagerImpl.h

## 文件概述
全局实体管理器，负责 Entity 的创建、销毁和生命周期管理。

## 核心类/结构体/函数
- **EntityManager**: 实体管理器（单例）
  - `get()`: 获取全局实例
  - `create(n, entities)`: 批量创建实体
  - `destroy(n, entities)`: 批量销毁实体
  - `isAlive(e)`: 检查实体是否存活
  - `Listener`: 实体销毁监听器接口
- **EntityManagerImpl**: 内部实现类

## 关键实现逻辑
- Entity ID = (generation << 17) | index，最多支持 131071 个同时存活的实体
- 使用 generation 计数器检测已销毁实体（弱引用语义）
- 维护空闲索引队列 (deque)，索引回收延迟 (MIN_FREE_INDICES=1024) 以降低 ABA 风险
- 全局实例通过 placement new 分配，故意泄漏以确保存活时间最长

## 依赖关系
- `utils/Entity.h`, `utils/Mutex.h`, `utils/FixedCapacityVector.h`, `utils/CallStack.h`
