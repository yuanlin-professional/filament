# Wireframe

## 文件概述
Wireframe 提供调试用的包围盒线框渲染功能。它遍历 asset 的变换层次结构，为每个可渲染实体的轴对齐包围盒生成 LINES 类型的线框网格。

## 核心结构体

- **`Wireframe`**: 包含线框渲染所需的 Entity、VertexBuffer 和 IndexBuffer。持有对 FFilamentAsset 的 const 引用。

## 关键实现逻辑

- **两遍遍历**: 第一遍递归统计可渲染实体数量以预分配缓冲；第二遍递归填充顶点和索引数据。
- **线框生成**: 对每个可渲染实体，获取其 AABB 的 8 个角点，经世界变换后写入顶点缓冲。生成 12 条边（24 个索引）构成长方体线框。
- **资源管理**: 析构时销毁 Entity、VertexBuffer 和 IndexBuffer。
- 通过 `FilamentAsset::getWireframe()` 懒创建。

## 依赖关系
- `FFilamentAsset.h`
- `filament/Box.h`, `filament/Engine.h`, `filament/VertexBuffer.h`, `filament/IndexBuffer.h`
- `filament/RenderableManager.h`, `filament/TransformManager.h`
- `utils/EntityManager.h`
- `math/vec3.h`, `math/mat4.h`
