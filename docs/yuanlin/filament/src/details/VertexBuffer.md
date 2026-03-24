# VertexBuffer

## 文件概述

`FVertexBuffer` 是 `VertexBuffer` 公共 API 的私有实现类，管理 GPU 顶点缓冲区。支持多缓冲区布局、顶点属性声明、BufferObject 绑定、异步数据上传以及高级蒙皮（每顶点多于 4 个骨骼权重）。

## 核心类/结构体

### `FVertexBuffer`
- 继承自 `VertexBuffer`
- 持有 `VertexBufferInfoHandle`（顶点属性布局描述）和 `VertexBufferHandle`（实际缓冲区）
- `mBufferObjects` 数组存储每个缓冲区槽位的 BufferObject 句柄
- `mDeclaredAttributes` 位集记录已声明的顶点属性（POSITION、UV 等）
- `mCreationComplete` 原子标志支持异步创建

## 关键实现逻辑

- **setBufferAt()** -- 将数据写入指定缓冲区索引位置
- **setBufferAtAsync()** -- 通过 `CallbackAdapter` 实现异步数据上传
- **setBufferObjectAt()** -- 绑定外部 `FBufferObject` 到指定缓冲区槽位，支持共享缓冲区
- **updateBoneIndicesAndWeights()** -- 更新高级蒙皮的骨骼索引和权重数据
- **构造** -- 支持从 Builder 创建和从已有 VertexBuffer 克隆两种方式

## 依赖关系

- `details/Engine.h` -- 引擎驱动 API
- `backend/DriverEnums.h` -- 顶点属性类型和格式枚举
- `utils/bitset.h` -- 属性位集

## 与公共 API 的关系

直接对应 `filament::VertexBuffer`。通过 `VertexBuffer::Builder` 创建，与 `IndexBuffer` 一起通过 `RenderableManager` 设置到可渲染组件上。
