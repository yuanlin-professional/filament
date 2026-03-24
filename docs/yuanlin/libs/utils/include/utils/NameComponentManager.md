# NameComponentManager.h / NameComponentManager.cpp

## 文件概述
实体名称组件管理器，允许为 ECS 中的实体关联字符串标签。

## 核心类/结构体/函数
- **NameComponentManager**: 名称组件管理器
  - `addComponent(e)`: 为实体添加名称组件
  - `removeComponent(e)`: 移除名称组件
  - `setName(instance, name)`: 设置名称
  - `getName(instance)`: 获取名称
  - `getInstance(e)`: 获取实体的临时实例句柄
  - `gc(em)`: 垃圾回收已销毁实体的组件

## 关键实现逻辑
- 继承自 SingleInstanceComponentManager<CString>，使用 SoA 存储
- Instance 是临时句柄，不应长期持有

## 依赖关系
- `utils/CString.h`, `utils/Entity.h`, `utils/EntityInstance.h`, `utils/SingleInstanceComponentManager.h`
