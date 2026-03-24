# PipelineState.h

## 文件概述

定义了渲染管线状态相关的结构体，包括管线布局（`PipelineLayout`）和完整管线状态（`PipelineState`）。这些结构体用于在绘制调用时指定渲染管线的完整配置。

## 核心结构体

### PipelineLayout

描述管线使用的描述符集布局。

```cpp
struct PipelineLayout {
    using SetLayout = std::array<Handle<HwDescriptorSetLayout>, MAX_DESCRIPTOR_SET_COUNT>;
    SetLayout setLayout;  // 最多 4 个描述符集布局
};
```

### PipelineState

完整的渲染管线状态，包含绘制所需的所有状态信息。

| 字段 | 类型 | 说明 |
|------|------|------|
| `program` | `Handle<HwProgram>` | 着色器程序句柄 |
| `vertexBufferInfo` | `Handle<HwVertexBufferInfo>` | 顶点缓冲区信息 |
| `pipelineLayout` | `PipelineLayout` | 管线描述符集布局 |
| `rasterState` | `RasterState` | 光栅化状态 |
| `stencilState` | `StencilState` | 模板测试状态 |
| `polygonOffset` | `PolygonOffset` | 多边形偏移 |
| `primitiveType` | `PrimitiveType` | 图元类型（默认三角形） |

## 依赖关系

- `backend/DriverEnums.h` - 枚举和状态结构体定义
- `backend/Handle.h` - 句柄类型
