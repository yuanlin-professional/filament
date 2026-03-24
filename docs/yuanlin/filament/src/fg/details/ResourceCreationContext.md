# ResourceCreationContext

## 文件概述

`details/ResourceCreationContext.h` / `ResourceCreationContext.cpp` 定义了帧图资源创建/销毁时所需的上下文信息。

## 核心类/结构体

- **`ResourceCreationContext`**: 在 `FrameGraph::execute()` 阶段创建，作为参数传递给 `VirtualResource::devirtualize()` 和 `VirtualResource::destroy()`。
  - `fg`: FrameGraph 引用
  - `driver`: 后端 DriverApi 引用
  - `useProtectedMemory`: 是否使用受保护内存
  - `getTextureCache()`: 通过 FrameGraph 获取 TextureCacheInterface

## 关键实现逻辑

- 提供统一的上下文传递接口，避免 devirtualize/destroy 方法需要多个独立参数。
- `getTextureCache()` 实现在 .cpp 中以避免头文件依赖 FrameGraph.h。

## 依赖关系

- `backend/DriverApiForward.h` - DriverApi 前向声明
- `fg/FrameGraph.h`（仅 .cpp）- 获取 TextureCache
