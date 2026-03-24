# Entity.h

## 文件概述
实体组件系统 (ECS) 中的实体句柄定义。Entity 是一个轻量级的 32 位标识符。

## 核心类/结构体/函数
- **Entity**: 实体句柄类（32 位）
  - `isNull()`: 检查是否为空实体
  - `getId()`: 获取内部标识符
  - `smuggle()` / `import()`: Java 互操作的序列化/反序列化
  - `Hasher`: 用于哈希容器的哈希函数对象

## 关键实现逻辑
- 内部标识符 mIdentity 编码了 generation 和 index 信息
- 构造函数为 private，只能通过 EntityManager 创建

## 依赖关系
- `utils/compiler.h`
