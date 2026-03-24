# DependencyGraph

## 文件概述
DependencyGraph 是一个内部依赖图，用于跟踪纹理加载状态以实现渲染实体的渐进式显示。它维护 Entity -> MaterialInstance -> Parameter -> Texture 的四层有向图，当一个实体的所有纹理依赖都就绪时，该实体被标记为可渲染。

## 核心类/结构体

- **`DependencyGraph`**: 主类，包含以下关键方法：
  - `addEdge()`: 添加三种类型的边（Entity-Material、Material-Parameter、Texture-Material-Parameter）
  - `commitEdges()`: 提交边集合，触发无纹理依赖的实体立即就绪
  - `markAsReady(Texture*)`: 标记纹理已解码完成，级联检查关联的材质和实体
  - `popRenderables()`: 弹出已就绪的可渲染实体
  - `disableProgressiveReveal()`: 禁用渐进式显示，将所有未就绪实体立即标记为就绪

## 关键实现逻辑

- **就绪检查**: 当纹理就绪时，遍历其关联的材质。对每个材质，检查所有纹理参数是否都已就绪。若材质就绪，递增其关联实体的就绪材质计数。当实体的所有材质都就绪时，推入就绪队列。
- **TextureNode**: 使用 unique_ptr 封装纹理就绪状态，允许多个节点通过稳定指针引用同一纹理状态。
- **禁用模式**: 在 `disableProgressiveReveal()` 后添加的 Entity-Material 边会立即将实体推入就绪队列，不再增长图结构。

## 依赖关系
- `utils/Entity.h`
- `tsl/robin_map.h`, `tsl/robin_set.h`
- `filament/MaterialInstance`, `filament/Texture`（前向声明）
