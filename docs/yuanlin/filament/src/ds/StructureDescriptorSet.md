# StructureDescriptorSet

## 文件概述

`StructureDescriptorSet.h` / `StructureDescriptorSet.cpp` 实现了结构通道（Structure Pass）的描述符集管理。结构通道负责生成深度/法线等屏幕空间结构信息，供后续 SSAO、SSR 等效果使用。

## 核心类/结构体

- **`StructureDescriptorSet`**: 结构通道的描述符集管理器。
  - `prepareCamera()`: 设置相机参数
  - `prepareLodBias()`: 设置 LOD 偏差
  - `prepareViewport()`: 设置视口参数
  - `bind()`: 提交 UBO（如果需要）并绑定描述符集

## 关键实现逻辑

- 结构通道的 PerViewUib 只需要填充相机和视口相关的字段，比颜色通道简单得多。
- `bind()` 方法组合了 commit 和 bind 操作，简化调用流程。
- 与 `PerViewDescriptorSetUtils` 共享通用的 uniform 填充逻辑。

## 依赖关系

- `ds/DescriptorSet.h` - 描述符集基础
- `ds/TypedUniformBuffer.h` - 类型化 UBO
- `private/filament/UibStructs.h` - `PerViewUib`
- `math/vec2.h`, `math/vec4.h` - 数学类型
