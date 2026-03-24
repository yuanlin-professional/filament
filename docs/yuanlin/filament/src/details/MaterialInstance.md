# MaterialInstance

## 文件概述

`FMaterialInstance` 是 `MaterialInstance` 公共 API 的私有实现类，代表材质的一个参数化实例。每个实例拥有独立的 uniform 数据、纹理绑定、光栅化状态（裁剪模式、深度/模板测试、颜色写入等），可在不重新编译着色器的情况下改变材质外观。

## 核心类/结构体

### `FMaterialInstance`
- 继承自 `MaterialInstance`
- 持有 `UniformBuffer` 存储 uniform 数据
- `DescriptorSet` 管理纹理/采样器绑定
- 支持 UBO 批处理模式（通过 `BufferAllocator::AllocationId` 共享 UBO）
- 丰富的光栅化状态：CullingMode、DepthFunc、StencilState、PolygonOffset 等

## 关键实现逻辑

- **commit()** -- 将 uniform 数据和纹理绑定提交到驱动，支持独立 UBO 和共享 UBO 两种模式
- **use()** -- 在渲染时绑定描述符集
- **参数设置** -- 模板化的 `setParameterImpl()` 支持标量/向量/矩阵类型及数组
- **剪裁矩形** -- `setScissor()`/`unsetScissor()` 管理 per-instance 裁剪区域
- **模板测试** -- 完整的模板状态管理（比较函数、操作、引用值、读写掩码，支持前后面独立设置）
- **排序键** -- `mMaterialSortingKey` 用于渲染排序优化

## 依赖关系

- `details/Engine.h` -- 引擎和驱动 API
- `details/BufferAllocator.h` -- UBO 批处理分配
- `ds/DescriptorSet.h` -- 描述符集管理
- `UniformBuffer.h` -- uniform 数据缓冲

## 与公共 API 的关系

直接对应 `filament::MaterialInstance`。通过 `Material::createInstance()` 创建，绑定到 `RenderableManager` 的图元上。每个 Material 有一个默认实例。
