# FNodeManager（内部头文件）

## 文件概述
FNodeManager.h 是 NodeManager 的内部实现头文件，基于 Filament 的 SingleInstanceComponentManager 模板实现高效的 SoA（Structure of Arrays）数据布局。

## 核心类

- **`FNodeManager`**: 继承 NodeManager，使用 Sim（SingleInstanceComponentManager 特化）管理组件数据：
  - `MORPH_TARGET_NAMES`: FixedCapacityVector<CString>（16 字节）
  - `EXTRAS_STRING`: CString（8 字节）
  - `SCENE_MEMBERSHIP`: SceneMask/bitset32（4 字节）
  - 总计约 28 字节每组件

## 关键实现逻辑

- `create()` 在添加组件前检查是否已存在，若存在先销毁旧组件。
- `gc()` 垃圾回收，配合 EntityManager 清理已销毁实体的组件。
- `terminate()` 在 debug 模式下报告泄漏组件数量并清理。
- 使用 Proxy 联合体实现字段访问，通过 Field<INDEX> 模板实现类型安全的 SoA 元素访问。

## 依赖关系
- `gltfio/NodeManager.h`
- `utils/SingleInstanceComponentManager.h`, `utils/Entity.h`
- `downcast.h`
