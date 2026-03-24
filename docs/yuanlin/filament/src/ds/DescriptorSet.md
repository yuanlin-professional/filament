# DescriptorSet

## 文件概述

`DescriptorSet.h` / `DescriptorSet.cpp` 实现了 Filament 的描述符集封装，管理 UBO（Uniform Buffer Object）和采样器描述符的绑定。这是着色器资源绑定系统的核心组件。

## 核心类/结构体

- **`DescriptorSet`**: 描述符集管理类。
  - **`Desc`**: 内部联合体，可以存储 buffer 描述符（boh + offset + size）或 texture 描述符（th + SamplerParams）。
  - `mDescriptors`: 固定容量向量，按 binding 索引存储所有描述符。
  - `mDirty`: 64 位脏标志位集，标记哪些描述符被修改过。
  - `mValid`: 64 位有效标志位集，标记哪些描述符已被设置。
  - `mDescriptorSetHandle`: 后端描述符集句柄。

## 关键实现逻辑

- **延迟提交 (`commit`/`commitSlow`)**: 只有在有脏标志时才触发提交。提交时销毁旧的描述符集，创建新的，然后遍历所有有效描述符更新到新集合中。
- **绑定 (`bind`)**: 将描述符集绑定到指定的绑定点，支持动态偏移数组。Debug 模式下检测在 renderpass 期间修改描述符的情况。
- **设置缓冲/采样器**: `setBuffer` 和 `setSampler` 会进行描述符类型验证，并在值变化时设置脏标志。`setSampler` 还验证深度描述符的比较模式一致性。
- **复制 (`duplicate`)**: 复制描述符数据，将所有有效描述符标记为脏以确保在下次 commit 时更新。
- **纹理类型兼容性检查 (`isTextureCompatibleWithDescriptor`)**: 验证纹理类型（float/int/uint/depth）与描述符类型的匹配，以及采样器维度（2D/2DArray/Cube 等）的匹配。

## 依赖关系

- `ds/DescriptorSetLayout.h` - 描述符集布局
- `private/filament/EngineEnums.h` - `DescriptorSetBindingPoints` 枚举
- `backend/DescriptorSetOffsetArray.h` - 动态偏移数组
- `backend/Handle.h`, `backend/DriverEnums.h` - 后端句柄和枚举
- `utils/bitset.h` - 64 位位集
