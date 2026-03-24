# Sphere.h / Sphere.cpp

## 文件概述
基于 IcoSphere 细分球体创建 Filament 可渲染球体对象，支持位置和半径调整。

## 核心类

### `Sphere`
- **构造函数**: 使用 2 级细分的 IcoSphere，通过 SurfaceOrientation 计算切线空间四元数，构建顶点/索引缓冲区和 Renderable
- `setPosition(position)` - 通过修改 TransformManager 的平移分量设置位置
- `setRadius(radius)` - 通过修改 TransformManager 的缩放分量设置半径
- `getMaterialInstance()` / `getSolidRenderable()` - 获取材质实例和实体

## 关键实现逻辑
- 使用全局静态 `Geometry` 对象缓存球体几何数据（注意：会造成内存泄漏，因为没有合适的释放时机）
- 切线空间使用 `geometry::SurfaceOrientation::Builder` 构建，存储为 SHORT4 格式

## 依赖关系
- `filamentapp/IcoSphere.h` - 球体几何生成
- `geometry/SurfaceOrientation.h` - 切线空间计算
- `filament/VertexBuffer.h`, `filament/IndexBuffer.h`, `filament/RenderableManager.h`
