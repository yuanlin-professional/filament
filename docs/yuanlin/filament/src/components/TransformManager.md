# TransformManager

## 文件概述

`TransformManager.h` / `TransformManager.cpp` 实现了 Filament 引擎的变换组件管理器，负责管理实体的层级变换关系（父子关系）以及本地/世界坐标变换矩阵的计算。

## 核心类/结构体

- **`FTransformManager`**: 继承自公共 `TransformManager`，管理实体的空间变换。
  - **`Sim`**: SoA 存储结构，包含 8 个字段：
    - `LOCAL` / `WORLD`: 本地和世界 4x4 变换矩阵（`mat4f`）
    - `LOCAL_LO` / `WORLD_LO`: 高精度平移的低位部分（`float3`），用于双精度平移
    - `PARENT` / `FIRST_CHILD` / `NEXT` / `PREV`: 层级关系链表指针（`Instance`）

## 关键实现逻辑

- **层级关系**: 使用侵入式双向链表维护父子关系。每个节点有 parent、firstChild、next、prev 四个链接。`insertNode` 将节点插入为父节点的第一个子节点。
- **变换更新 (`updateNodeTransform`)**: 计算世界变换时，根据父节点的世界变换和自身的本地变换相乘。递归更新所有子节点（`transformChildren`）。
- **高精度平移 (`AccurateTranslations`)**: 启用后，将平移分为 float 高位和 float 低位两部分存储，计算世界变换时使用 `mat4`（double）进行乘法，避免远距离场景中的精度丢失。
- **批量事务 (`openLocalTransformTransaction` / `commitLocalTransformTransaction`)**: 打开事务后，`setTransform` 不会立即更新世界变换，而是在 `commit` 时通过 `computeAllWorldTransforms` 一次性计算所有世界变换。`commit` 时会确保子节点始终排在父节点之后（通过 `swapNode`）。
- **节点交换 (`swapNode`)**: 交换两个节点的数据和链表引用，需要使用临时存储来正确修复链表指针。
- **验证 (`validateNode`)**: 仅在 Debug 模式下运行，检查链表完整性（无自引用、父子关系一致等）。

## 依赖关系

- `filament/TransformManager.h` - 公共 API 定义和 `children_iterator`
- `utils/SingleInstanceComponentManager.h` - ECS 组件管理基础
- `math/mat4.h` - 4x4 变换矩阵（float 和 double 版本）
- `downcast.h` - FILAMENT_DOWNCAST 宏
