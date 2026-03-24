# PostProcessDescriptorSet

## 文件概述

`PostProcessDescriptorSet.h` / `PostProcessDescriptorSet.cpp` 实现了后处理通道所需的描述符集管理，提供后处理着色器的帧级 uniform 数据绑定。

## 核心类/结构体

- **`PostProcessDescriptorSet`**: 管理后处理通道的单一描述符集和布局。
  - `mDescriptorSetLayout`: 后处理专用的描述符集布局
  - `mDescriptorSet`: 描述符集实例
  - `setFrameUniforms()`: 将 PerViewUib 数据绑定到描述符集
  - `bind()`: 绑定描述符集以供后处理着色器使用
  - `getLayout()`: 获取布局引用

## 关键实现逻辑

- 后处理通道使用独立于颜色通道的描述符集布局，因为后处理着色器需要不同的资源绑定。
- 初始化通过 `init(FEngine&)` 延迟执行，终止通过 `terminate()` 释放后端资源。

## 依赖关系

- `ds/DescriptorSet.h`, `ds/DescriptorSetLayout.h` - 描述符集基础
- `ds/TypedUniformBuffer.h` - `TypedUniformBuffer<PerViewUib>`
- `private/filament/UibStructs.h` - `PerViewUib`
