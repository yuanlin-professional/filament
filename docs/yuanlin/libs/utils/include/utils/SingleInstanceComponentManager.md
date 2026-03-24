# SingleInstanceComponentManager.h

## 文件概述
单实例组件管理器基类，为 ECS 组件提供基于 Structure-of-Arrays 的存储和垃圾回收。

## 核心类/结构体/函数
- **SingleInstanceComponentManager<Elements...>**: 模板基类
  - 使用 StructureOfArrays 存储组件数据
  - Entity -> Instance 映射使用 robin_map
  - `hasComponent(e)`: 检查实体是否有组件
  - `getInstance(e)`: 获取实体的实例索引
  - `gc(em, destroyer)`: 垃圾回收已销毁实体的组件

## 关键实现逻辑
- 组件数据紧凑存储在 SoA 中，缓存友好
- 使用 swap-and-pop 策略移除组件，保持数组密集

## 依赖关系
- `utils/Entity.h`, `utils/EntityInstance.h`, `utils/EntityManager.h`, `utils/StructureOfArrays.h`
