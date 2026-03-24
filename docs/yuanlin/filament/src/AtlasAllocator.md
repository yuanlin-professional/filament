# AtlasAllocator.h / AtlasAllocator.cpp

## 文件概述

实现基于四叉树（QuadTree）的 2D 图集空间分配器，属于伙伴分配器（Buddy Allocator）的变体。用于管理纹理图集（如阴影贴图图集）中的空间分配。所有分配必须为正方形且大小为 2 的幂次。分配器本身不管理实际内存，只管理抽象 2D 图像中的区域。

## 核心类/结构体/函数

### `AtlasAllocator` 类

- **`Node`** -- 四叉树节点数据结构，使用位域追踪分配状态（`allocated`）和子节点数量（`children`，0-4）。
- **`Allocation`** -- 分配结果结构体，包含 `viewport`（分配区域的位置和大小）、`layer`（纹理数组层索引）、`code`（Morton 码，用于释放）和 `level`（四叉树层级）。
- **`allocate(size_t textureSize)`** -- 分配指定大小的正方形区域，使用"最佳适配"策略。
- **`free(Allocation const&)`** -- 释放分配并自动合并空闲兄弟节点（标准伙伴分配器行为）。
- **`clear(size_t maxTextureSize)`** -- 重置分配器。

### 辅助函数

- **`unmorton(uint16_t)`** -- 将 Morton 码解码为 2D 坐标 (x, y)，用于从四叉树位置计算纹理中的实际位置。

## 关键实现逻辑

- **四叉树深度分层**：`LAYERS_DEPTH=3` 控制纹理数组的层数（最多 64 层），`QUAD_TREE_DEPTH=4` 控制每层内的细分级别（支持 4 种大小）。
- **最佳适配分配**：`allocateInLayer` 遍历四叉树，优先选择已有子节点的节点进行分裂，以保留大的连续块。
- **合并释放**：`free` 在释放节点后沿树向上遍历，递减父节点的子节点计数，当所有子节点都被释放时自动合并为更大的可用块。
- **Morton 编码/解码**：通过位操作实现 2D 坐标与一维索引之间的快速转换。

## 依赖关系

- `utils/QuadTree.h` -- 提供四叉树数组的底层实现
- `filament/Viewport.h` -- Viewport 结构体定义
- `private/filament/EngineEnums.h` -- 引擎枚举常量（如 `CONFIG_MAX_SHADOW_LAYERS`）
- `utils/compiler.h`、`utils/algorithm.h`、`utils/debug.h` -- 工具函数

## 被引用关系

主要被阴影贴图（ShadowMap）系统引用，用于在阴影图集纹理中高效分配和管理各光源的阴影贴图区域。
