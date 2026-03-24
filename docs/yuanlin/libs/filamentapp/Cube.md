# Cube.h / Cube.cpp

## 文件概述
实现一个立方体可渲染对象，用于可视化相机视锥体和 AABB 包围盒。同时提供实体填充和线框两种渲染模式。

## 核心类

### `Cube`
- **构造函数**: 接收 Engine、Material 和颜色，创建包含 8 个顶点的顶点缓冲区和索引缓冲区，分别构建实体填充和线框两个 Renderable。
- `mapFrustum(engine, camera)` - 根据相机的投影矩阵和模型矩阵计算变换，将立方体映射为相机视锥体的形状。
- `mapFrustum(engine, transform)` - 直接用给定的变换矩阵设置实体和线框的 TransformManager。
- `mapAabb(engine, box)` - 将立方体映射为给定 AABB 包围盒的形状。

## 关键实现逻辑
- 顶点数据为单位立方体 [-1,1] 的 8 个角点
- 索引数据前 36 个为三角形面片（实体），后 24 个为线段（线框）
- 填充模式使用透明度 0.05，线框使用透明度 0.25

## 依赖关系
- `filament/VertexBuffer.h`, `filament/IndexBuffer.h`, `filament/RenderableManager.h`
- `filament/TransformManager.h`, `filament/Camera.h`
- `utils/EntityManager.h`
