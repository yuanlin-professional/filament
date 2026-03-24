# RenderableManager.cpp

## 文件概述
RenderableManager.cpp 实现了可渲染对象管理器的公共 API。它管理所有可渲染实体的属性，包括几何体、材质、阴影、骨骼动画、变形目标等。

## 核心类/结构体/函数
- **RenderableManager** - 可渲染对象管理器公共接口
  - `hasComponent()` / `getInstance()` / `getEntity()` - 实体-组件查询
  - `setAxisAlignedBoundingBox()` / `getAxisAlignedBoundingBox()` - 包围盒管理
  - `setLayerMask()` / `getLayerMask()` - 图层掩码控制
  - `setPriority()` / `setChannel()` - 渲染优先级和通道
  - `setCulling()` / `isCullingEnabled()` - 裁剪开关
  - `setCastShadows()` / `setReceiveShadows()` / `setScreenSpaceContactShadows()` - 阴影配置
  - `setMaterialInstanceAt()` / `getMaterialInstanceAt()` / `clearMaterialInstanceAt()` - 图元材质绑定
  - `setBlendOrderAt()` / `setGlobalBlendOrderEnabledAt()` - 透明排序控制
  - `setGeometryAt()` - 设置图元几何数据（顶点缓冲、索引缓冲、图元类型）
  - `setBones()` / `setSkinningBuffer()` - 骨骼蒙皮
  - `setMorphWeights()` / `setMorphTargetBufferOffsetAt()` - 变形目标权重
  - `setLightChannel()` / `setFogEnabled()` - 光照通道和雾效

## 关键实现逻辑
所有方法通过 `downcast` 委托给内部 `FRenderableManager`。多实例（`getInstanceCount`）和多层级（level 参数，通常为 0）的设计支持实例化渲染。

## 依赖关系
- `components/RenderableManager.h` - 内部实现
- `details/Engine.h` / `details/VertexBuffer.h` / `details/IndexBuffer.h` / `details/Material.h`

## 被引用关系
- 用户层代码通过 RenderableManager 配置可渲染实体的属性
- RenderPass 在生成绘制命令时读取 RenderableManager 的数据
