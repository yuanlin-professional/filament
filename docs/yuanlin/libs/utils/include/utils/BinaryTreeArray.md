# BinaryTreeArray.h

## 文件概述
深度优先二叉树数组的遍历工具，用于高效的树结构操作。

## 核心类/结构体/函数
- **BinaryTreeArray**: 静态工具类
  - `count(height)`: 计算指定高度的完全二叉树节点数
  - `left(i, height)` / `right(i, height)`: 计算左右子节点索引
  - `traverse(height, leaf, node)`: 后序遍历二叉树，对叶子和内部节点调用回调

## 关键实现逻辑
- 使用显式栈模拟递归，避免函数调用开销
- 树存储在扁平数组中，通过索引计算子节点位置

## 依赖关系
- `utils/compiler.h`
