# FrameGraph

## 文件概述

`FrameGraph.h` / `FrameGraph.cpp` 是帧图系统的核心实现，提供了一个声明式的渲染通道管理框架。帧图允许用户声明渲染通道及其资源依赖关系，自动进行资源生命周期管理和未使用通道的裁剪。

## 核心类/结构体

- **`FrameGraph`**: 帧图的核心类。
  - **`Builder`**: 内部类，在 setup 阶段用于声明资源的创建、读取、写入和渲染通道。提供 `create`、`read`、`write`、`sample`、`declareRenderPass`、`sideEffect` 等方法。
  - **`ResourceSlot`**: 内部结构，维护资源的版本号和索引（rid 指向 VirtualResource，nid 指向 ResourceNode，sid 用于子资源读写分离）。
  - **`Mode`**: 枚举，支持 `UNPROTECTED` 和 `PROTECTED`（受保护内存）两种模式。
- 内部存储使用线性分配器 Arena（262KB）来分配 PassNode、ResourceNode、VirtualResource 等对象。

## 关键实现逻辑

- **添加通道 (`addPass`)**: 模板方法，创建 `FrameGraphPass<Data, Execute>` 对象，调用 setup lambda 声明资源依赖，然后将 PassNode 加入列表。
- **编译 (`compile`)**: 三个阶段：(1) 依赖图裁剪（`DependencyGraph::cull`）；(2) 将活跃通道排在前面，计算资源的首次/末次使用通道，构建 devirtualize/destroy 列表；(3) 解析所有 ResourceNode 的使用标志位。
- **执行 (`execute`)**: 遍历活跃通道，先 devirtualize（创建具体资源），然后执行通道回调，最后 destroy（销毁具体资源）。
- **资源版本控制**: 每次 `write` 操作会创建新的 ResourceNode 版本，确保 SSA（静态单赋值）形式的资源追踪。
- **子资源支持**: `createSubresource` 支持纹理 mip level / layer 级别的资源追踪，读写操作会自动建立与父资源的依赖关系。
- **资源转发 (`forwardResource`)**: 允许一个资源替换另一个资源，被替换的资源句柄永久失效。

## 依赖关系

- `fg/Blackboard.h` - 资源共享黑板
- `fg/FrameGraphId.h`, `fg/FrameGraphPass.h`, `fg/FrameGraphTexture.h` - 帧图基础设施
- `fg/details/DependencyGraph.h`, `fg/details/Resource.h` - 依赖图和资源抽象
- `Allocators.h` - 线性分配器
- `backend/DriverEnums.h`, `backend/Handle.h` - 后端枚举和句柄
