# TangentsJob

## 文件概述
TangentsJob 是一个内部辅助结构，负责从 cgltf primitive 生成 Filament 的 TANGENTS 属性（表面方向四元数）。设计为可在 JobSystem 作业中并行运行。支持基础网格和变形目标的切线生成。

## 核心结构体

- **`TangentsJob::InputParams`**: 输入参数，包含 cgltf_primitive 指针和可选的变形目标索引。
- **`TangentsJob::Context`**: 客户端上下文（VertexBuffer/MorphTargetBuffer 指针、偏移和槽位），不被计算逻辑使用。
- **`TangentsJob::OutputParams`**: 输出参数，包含顶点数和 malloc 分配的 short4 四元数数组。
- **`TangentsJob::Params`**: 将输入、上下文和输出捆绑为一个结构体。

## 关键实现逻辑

- **属性提取**: 从 cgltf primitive 和可选的变形目标中提取法线、切线、位置、UV 和索引数据。
- **变形目标处理**: 若处理变形目标，将 delta 值加到基础属性上。
- **SurfaceOrientation**: 使用 `geometry::SurfaceOrientation::Builder` 构建表面方向，输入位置、法线、切线（可选）、UV（可选）和三角形索引，输出压缩的 short4 四元数。
- **无索引处理**: 若 primitive 没有索引缓冲，生成顺序索引（0,1,2,3,...）。

## 依赖关系
- `cgltf.h`
- `geometry/SurfaceOrientation.h`
- `math/vec4.h`, `math/vec3.h`, `math/vec2.h`
- `filament/VertexBuffer`, `filament/MorphTargetBuffer`（前向声明）
