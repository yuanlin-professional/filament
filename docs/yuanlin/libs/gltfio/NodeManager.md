# NodeManager

## 文件概述
NodeManager 是一个 ECS 组件管理器，用于为实体附加 glTF 特有的节点信息。存储变形目标名称、extras 字符串和场景归属掩码等数据。由 gltfio 内部创建，但向用户公开以允许查询。

## 核心类/结构体

- **`NodeManager`**: 公共接口，提供 `hasComponent()`、`getInstance()`、`create()`、`destroy()` 以及变形目标名称、extras、场景归属的存取方法。
- **`FNodeManager`**: 内部实现，基于 `SingleInstanceComponentManager` SoA（Structure of Arrays）存储：
  - `MORPH_TARGET_NAMES`: FixedCapacityVector<CString>（变形目标名称列表）
  - `EXTRAS_STRING`: CString（glTF extras JSON 字符串）
  - `SCENE_MEMBERSHIP`: SceneMask/bitset32（场景归属位掩码，最多 32 个场景）

## 关键实现逻辑

- 使用 Filament 的 `SingleInstanceComponentManager` 模板实现高效的 SoA 数据布局。
- `terminate()` 在 debug 模式下报告泄漏的组件数量。
- 通过 downcast 宏实现公共接口到内部实现的静态转发。

## 依赖关系
- `filament/FilamentAPI.h`
- `utils/SingleInstanceComponentManager.h`, `utils/Entity.h`, `utils/CString.h`, `utils/bitset.h`
- `downcast.h`
