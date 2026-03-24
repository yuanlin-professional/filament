# VertexBuffer.cpp

## 文件概述
VertexBuffer.cpp 实现了顶点缓冲区的公共 API。VertexBuffer 存储网格的顶点属性数据（位置、法线、UV、颜色等），是几何渲染的基础资源。

## 核心类/结构体/函数
- **VertexBuffer** - 顶点缓冲区公共接口
  - `getVertexCount()` - 获取顶点数量
  - `setBufferAt()` - 设置指定缓冲区槽位的数据（同步上传）
  - `setBufferAtAsync()` - 异步上传缓冲区数据，返回 AsyncCallId
  - `setBufferObjectAt()` - 绑定 BufferObject 到指定槽位
  - `setBufferObjectAtAsync()` - 异步绑定 BufferObject
  - `isCreationComplete()` - 检查顶点缓冲区是否创建完成

## 关键实现逻辑
通过 `downcast` 委托给内部 `FVertexBuffer`。支持同步和异步两种数据上传模式。`setBufferObjectAt()` 允许使用共享的 BufferObject，减少内存占用。

## 依赖关系
- `details/VertexBuffer.h` / `details/Engine.h` - 内部实现

## 被引用关系
- `RenderableManager` - 通过 `setGeometryAt()` 绑定 VertexBuffer 到图元
- 用户层代码创建并填充顶点数据
