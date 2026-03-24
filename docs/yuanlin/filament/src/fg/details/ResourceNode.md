# ResourceNode

## 文件概述

`details/ResourceNode.h` / `ResourceNode.cpp` 定义了帧图中资源节点，表示资源在依赖图中的一个版本。每次写入操作会创建新的 ResourceNode 版本。

## 核心类/结构体

- **`ResourceNode`**: 继承自 `DependencyGraph::Node`，表示资源的一个版本。
  - `resourceHandle`: 关联的帧图资源句柄
  - `mWriterPass`: 写入该版本的通道边（ResourceEdgeBase*，至多一个写者）
  - `mReaderPasses`: 读取该版本的通道边列表
  - `mParentHandle`: 父资源句柄
  - `mParentReadEdge` / `mParentWriteEdge`: 父子资源间的读写依赖边
  - `mForwardedEdge`: 资源转发依赖边

## 关键实现逻辑

- **读写追踪**: `setIncomingEdge` 设置写入者（断言只能设一次），`addOutgoingEdge` 添加读取者。
- **活跃读写检测**: `hasActiveReaders()` 和 `hasActiveWriters()` 通过依赖图查询（而非直接使用内部列表），以正确处理子资源的传递关系。
- **祖先节点查找 (`getAncestorNode`)**: 沿父节点链向上遍历，找到最顶层的资源节点。
- **父子依赖**: `setParentReadDependency` 创建从父节点到子节点的边（读取传播），`setParentWriteDependency` 创建从子节点到父节点的边（写入传播）。
- **使用标志解析 (`resolveResourceUsage`)**: 委托给关联的 VirtualResource 的 `resolveUsage` 方法。
- **析构**: 负责销毁所有关联的边（通过 VirtualResource::destroyEdge 进行类型安全的删除）。

## 依赖关系

- `fg/details/DependencyGraph.h` - 依赖图基类
- `fg/details/Utilities.h` - `Vector` 类型别名
- `fg/FrameGraph.h` - FrameGraph 访问
