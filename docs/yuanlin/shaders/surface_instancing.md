# surface_instancing.glsl

## 文件概述
GPU 实例化支持。处理实例化渲染时的每实例数据获取，支持标准实例化和实例缓冲区两种模式。

## 核心功能
- 从实例索引获取每实例的模型矩阵和属性
- 实例缓冲区模式下从 SSBO（着色器存储缓冲对象）读取数据
- 立体渲染实例化的逻辑索引计算

## 依赖关系
- `surface_types.glsl` - PerRenderableData
