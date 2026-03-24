# RenderPrimitive.h / RenderPrimitive.cpp

## 文件概述
FRenderPrimitive 表示一个可渲染图元，是 RenderableManager 中每个图元条目的内部表示。它封装了顶点缓冲、索引缓冲、材质实例和渲染属性等信息。

## 核心类/结构体/函数
- **FRenderPrimitive** - 渲染图元内部类
  - `init()` - 从 FRenderableManager::Entry 初始化（材质实例、混合顺序、几何数据）
  - `set()` - 设置几何数据（图元类型、顶点缓冲、索引缓冲、偏移、计数）
  - `terminate()` - 释放驱动资源
  - 访问器：`getMaterialInstance()` / `getHwHandle()` / `getVertexBufferInfoHandle()` / `getIndexOffset()` / `getIndexCount()` / `getPrimitiveType()` / `getEnabledAttributes()` / `getBlendOrder()` / `getMorphingBufferOffset()`
  - 设置器：`setMaterialInstance()` / `setBlendOrder()` / `setGlobalBlendOrderEnabled()` / `setMorphingBufferOffset()`

## 关键实现逻辑
- 字段布局优化：前几个字段（mMaterialInstance、mHandle、mVertexBufferInfoHandle、mIndexOffset、mIndexCount）与 `RenderPass::PrimitiveInfo` 对齐，便于高效拷贝
- `set()` 在更新几何数据前会先销毁旧的 RenderPrimitive 句柄
- 混合顺序使用 15 位（`order & 0x7FFF`），最高位保留

## 依赖关系
- `details/MaterialInstance.h` - 材质实例
- `details/VertexBuffer.h` / `details/IndexBuffer.h` - 顶点和索引缓冲
- `components/RenderableManager.h` - FRenderableManager::Entry
- `backend/Handle.h` - 后端句柄

## 被引用关系
- `FRenderableManager` - 管理 FRenderPrimitive 的生命周期
- `RenderPass` - 在命令生成时读取 FRenderPrimitive 的数据
