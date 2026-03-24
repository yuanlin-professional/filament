# QuadTree.h

## 文件概述
基于 Morton 编码的四叉树实现，用于空间数据的层级组织。

## 核心类/结构体/函数
- **QuadTreeUtils::morton(x, y)**: 16 位 Morton 编码
- **QuadTreeArray<T, DEPTH>**: 基于数组的紧凑四叉树
  - 支持 get/set 操作
  - 自动处理层级节点的聚合

## 关键实现逻辑
- 使用 Morton 编码 (Z-order curve) 将 2D 坐标映射到 1D 数组索引
- 位交错技术实现高效的坐标编码/解码

## 依赖关系
- 无外部 utils 依赖
