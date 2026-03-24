# PerViewDescriptorSetUtils

## 文件概述

`PerViewDescriptorSetUtils.h` / `PerViewDescriptorSetUtils.cpp` 提供了一组静态工具方法，用于填充 `PerViewUib` uniform 结构体中的各种字段。这些方法被多个描述符集类（ColorPassDescriptorSet、StructureDescriptorSet 等）共享调用。

## 核心类/结构体

- **`PerViewDescriptorSetUtils`**: 纯静态方法的工具类。
  - `prepareCamera()`: 设置视图/投影/剪裁矩阵等相机相关 uniform
  - `prepareLodBias()`: 设置 LOD 偏差和导数缩放
  - `prepareViewport()`: 设置物理/逻辑视口参数
  - `prepareTime()`: 设置时间相关 uniform
  - `prepareMaterialGlobals()`: 设置全局材质参数

## 关键实现逻辑

- 将通用的 PerViewUib 填充逻辑从各个描述符集类中提取出来，避免代码重复。
- 所有方法都是静态的，直接操作 `PerViewUib&` 引用。

## 依赖关系

- `private/filament/UibStructs.h` - `PerViewUib` 结构
- `math/vec2.h`, `math/vec4.h` - 数学类型
