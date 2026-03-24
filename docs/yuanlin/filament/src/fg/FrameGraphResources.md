# FrameGraphResources

## 文件概述

`FrameGraphResources.h` / `FrameGraphResources.cpp` 定义了帧图 execute 阶段中用于访问具体资源的接口类。它在通道执行时提供资源的只读访问。

## 核心类/结构体

- **`FrameGraphResources`**: 资源访问器，在 execute 回调中作为参数传入。
  - **`RenderPassInfo`**: 包含渲染目标句柄和渲染通道参数。
  - `get<T>(handle)`: 获取具体资源引用
  - `getDescriptor<T>(handle)`: 获取资源描述符
  - `getSubResourceDescriptor<T>(handle)`: 获取子资源描述符
  - `getUsage<T>(handle)`: 获取资源使用标志
  - `detach<T>(handle, ...)`: 将资源从帧图中分离，转移生命周期管理权
  - `getRenderPassInfo(id)`: 获取渲染通道信息
  - `getTexture(handle)`: 便捷方法，获取纹理的后端句柄

## 关键实现逻辑

- 所有 `get` 方法内部都会验证：(1) 句柄已初始化；(2) 当前通道确实声明了对该资源的读写访问（通过 `mDeclaredHandles` 集合检查）。
- `detach()` 将资源标记为 `detached`，防止帧图在通道结束后销毁它。
- `getRenderPassInfo()` 仅在 `RenderPassNode` 中有效，通过静态转换获取渲染目标数据。

## 依赖关系

- `fg/details/Resource.h` - `Resource<T>` 模板和 `VirtualResource` 基类
- `fg/FrameGraphId.h` - 资源句柄类型
- `fg/details/PassNode.h`, `fg/details/ResourceNode.h` - 通道和资源节点
