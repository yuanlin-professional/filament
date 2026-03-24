# Grid.h / Grid.cpp

## 文件概述
实现 3D 线框网格的可渲染对象，主要用于可视化 Froxel 光照网格。支持自定义生成器函数来控制网格的非均匀分布。

## 核心类

### `Grid`
- **构造函数**: 创建材质实例和线框 Renderable 实体
- `update(width, height, depth)` - 使用均匀分布在 [-1,1] 范围内更新网格
- `update(width, height, depth, genWidth, genHeight, genDepth)` - 使用自定义生成器函数更新网格，允许非线性分布
- `mapFrustum(engine, camera)` / `mapFrustum(engine, transform)` - 映射到相机视锥体
- `mapAabb(engine, box)` - 映射到 AABB 包围盒

## 关键实现逻辑
- 每次 `update` 调用时销毁旧缓冲区并重新创建
- 生成 3D 网格顶点 `(width+1) * (height+1) * (depth+1)` 个
- 沿 X/Y/Z 三个轴方向生成线段索引
- 使用 `BufferDescriptor::make` 配合 lambda 回调安全管理内存

## 依赖关系
- `filament/VertexBuffer.h`, `filament/IndexBuffer.h`, `filament/RenderableManager.h`
- `filament/TransformManager.h`, `filament/Camera.h`
