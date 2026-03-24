# DependencyGraph

## 文件概述

`details/DependencyGraph.h` / `DependencyGraph.cpp` 实现了帧图系统底层的有向无环图（DAG）数据结构，支持节点引用计数和未使用节点的裁剪。

## 核心类/结构体

- **`DependencyGraph`**: DAG 实现类。
  - **`Node`**: 图节点基类。每个节点有唯一 ID、引用计数和 TARGET 标志（`0x80000000`，表示该节点是叶节点/目标节点，不可被裁剪）。提供 `makeTarget()`、`isCulled()`、`getRefCount()` 等方法，以及用于 Graphviz 可视化的虚方法。
  - **`Edge`**: 有向边，连接 `from` 和 `to` 两个节点。边创建后不可修改（const 成员），支持子类化携带额外数据。
  - `NodeContainer` / `EdgeContainer`: 使用 `FixedCapacityVector` 存储。

## 关键实现逻辑

- **裁剪算法 (`cull`)**: (1) 遍历所有边，增加 `from` 节点的引用计数；(2) 将引用计数为 0 的节点入栈；(3) 栈式处理：弹出节点，减少其所有入边 `from` 节点的引用计数，若降至 0 则入栈。最终引用计数为 0 的节点被标记为已裁剪。
- **环检测 (`isAcyclic`)**: 仅在 Debug 模式下有效。反复查找没有出边的叶节点，删除其所有边和自身，直到图为空（无环）或找不到叶节点（有环）。
- **Graphviz 导出**: Debug 模式下支持将图导出为 DOT 格式，有效边和无效边使用不同样式。
- **容量管理**: 节点和边容器在容量不足时翻倍扩展。

## 依赖关系

- `utils/FixedCapacityVector.h` - 固定容量向量容器
- `utils/CString.h`, `utils/ostream.h` - 字符串和输出流（用于 Graphviz 导出）
