# SibStructs.h

## 文件概述
定义采样器接口块（SIB, Sampler Interface Block）的结构体常量，为每视图和每可渲染对象的采样器提供索引常量。

## 核心类/结构体/函数
- **`PerViewSib`** - 每视图采样器索引（共 7 个）：
  - SHADOW_MAP(0)、IBL_DFG_LUT(1)、IBL_SPECULAR(2)、SSAO(3)、SSR(4)、STRUCTURE(5)、FOG(6)
- **`PerRenderPrimitiveMorphingSib`** - 变形渲染采样器索引（2 个）：POSITIONS(0)、TANGENTS(1)
- **`PerRenderPrimitiveSkinningSib`** - 骨骼蒙皮采样器索引（1 个）：BONE_INDICES_AND_WEIGHTS(0)

## 关键实现逻辑
纯常量定义文件，每个结构体的 `SAMPLER_COUNT` 记录该接口块中采样器的总数。

## 依赖关系
- `<stdint.h>`、`<stddef.h>`
