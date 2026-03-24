# PassNode

## 文件概述

`details/PassNode.h` / `PassNode.cpp` 定义了帧图中的通道节点层级结构，包含基类 `PassNode` 和两个具体实现 `RenderPassNode`（渲染通道）和 `PresentPassNode`（呈现通道）。

## 核心类/结构体

- **`PassNode`**: 通道节点基类，继承自 `DependencyGraph::Node`。
  - `mDeclaredHandles`: 已声明的资源句柄集合（用于 execute 阶段的访问验证）
  - `devirtualize` / `destroy`: 需要在通道执行前创建和执行后销毁的资源列表
  - `registerResource()`: 注册资源使用，更新资源的 first/last 通道和引用计数

- **`RenderPassNode`**: 渲染通道节点（final），持有用户传入的 `FrameGraphPassBase`。
  - **`RenderPassData`**: 渲染目标数据，包含名称、描述符、附件信息、incoming/outgoing ResourceNode 指针（用于计算 discard 标志）和后端 target/params。
  - `declareRenderTarget()`: 声明渲染目标，记录附件的输入/输出 ResourceNode
  - `resolve()`: 在 compile 阶段计算 discard/clear 标志、处理导入渲染目标

- **`PresentPassNode`**: 呈现通道节点（final），execute 为空操作，仅用于建立依赖关系。

## 关键实现逻辑

- **Discard 标志计算 (`resolve`)**: 对每个附件判断：如果写入后没有活跃读者，设置 `discardEnd`；如果没有前驱写入者，设置 `discardStart`。清除标志自动隐含 `discardStart`。
- **只读深度/模板**: 如果没有通道写入深度或模板，设置 `READONLY_DEPTH`/`READONLY_STENCIL`。
- **渲染目标创建/销毁 (`devirtualize`/`destroy`)**: 在 execute 中，非导入的渲染目标通过 TextureCache 动态创建和销毁。
- **导入渲染目标处理**: 使用 `ImportDescriptor` 覆盖计算的参数，并在第二次使用时清除清除标志。

## 依赖关系

- `fg/details/DependencyGraph.h` - 依赖图基类
- `fg/FrameGraph.h`, `fg/FrameGraphRenderPass.h` - 帧图和渲染通道描述
- `fg/details/Utilities.h` - `UniquePtr` 和 `Vector` 类型别名
- `TextureCache.h` - 纹理/渲染目标缓存
