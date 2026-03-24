# Resource

## 文件概述

`details/Resource.h` / `Resource.cpp` 定义了帧图资源的抽象层级结构，包括虚拟资源基类、类型化资源模板、导入资源和导入渲染目标。

## 核心类/结构体

- **`ResourceEdgeBase`**: 资源边的类型安全基类，继承自 `DependencyGraph::Edge`。
- **`VirtualResource`**: 所有帧图资源的抽象基类。
  - `parent`: 指向父资源（自引用表示顶层资源）
  - `refcount` / `first` / `last`: compile 阶段计算的引用计数和首末使用通道
  - 纯虚方法：`resolveUsage`、`devirtualize`、`destroy`、`destroyEdge`、`usageString`
  - `neededByPass()`: 递增引用计数并更新 first/last，同时向上传播到父资源

- **`Resource<RESOURCE>`**: 类型化资源模板，继承自 `VirtualResource`。
  - `resource`: 具体资源实例（devirtualize 后有效）
  - `usage`: 累积的使用标志
  - `descriptor` / `subResourceDescriptor`: 资源描述符
  - `detached`: 是否被分离（分离后不销毁）
  - **`ResourceEdge`**: 携带 `Usage` 的类型化边
  - `connect()` 两个重载：Pass->Resource（写入）和 Resource->Pass（读取）

- **`ImportedResource<RESOURCE>`**: 导入资源，devirtualize/destroy 为空操作，`isImported()` 返回 true。
- **`ImportedRenderTarget`**: 导入渲染目标，额外持有 `HwRenderTarget` 句柄和 `ImportDescriptor`。

## 关键实现逻辑

- **使用标志解析 (`resolveUsage`)**: 遍历所有有效的读取边和写入边，累积使用标志，并向上传播到父资源链。
- **devirtualize**: 顶层资源通过 `ResourceAllocator::create` 创建；子资源直接复用父资源的具体资源。
- **destroy**: 被分离的资源和子资源跳过销毁。
- **导入渲染目标**: 验证使用标志只能包含附件类型（COLOR/DEPTH/STENCIL），从附件标志推导使用标志。

## 依赖关系

- `fg/FrameGraphId.h`, `fg/FrameGraphTexture.h`, `fg/FrameGraphRenderPass.h` - 帧图类型
- `fg/details/ResourceAllocator.h`, `fg/details/ResourceCreationContext.h` - 资源分配
- `fg/details/DependencyGraph.h` - 依赖图
