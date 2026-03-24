# ShadowMapDescriptorSet

## 文件概述

`ShadowMapDescriptorSet.h` / `ShadowMapDescriptorSet.cpp` 实现了阴影贴图生成通道的描述符集管理。它不保留 uniform 数据的本地副本，而是直接写入命令流。

## 核心类/结构体

- **`ShadowMapDescriptorSet`**: 阴影贴图通道的描述符集管理器。
  - **`Transaction`**: 内部事务类，持有 `PerViewUib*` 指针，用于原子式地填充和提交 uniform 数据。只能由 ShadowMapDescriptorSet 创建。
  - 不保持 uniform 数据的影子副本，因此不支持部分更新。

## 关键实现逻辑

- 阴影贴图的 PerViewUib 直接在命令流中分配并写入，省去了一次内存复制。
- 使用事务模式确保所有需要的 uniform 数据一次性设置完成。
- 每个阴影贴图渲染通道可能需要不同的视图/投影矩阵，因此需要独立于主颜色通道的 uniform 管理。

## 依赖关系

- `ds/DescriptorSet.h`, `ds/DescriptorSetLayout.h` - 描述符集基础
- `private/filament/UibStructs.h` - `PerViewUib`
- `backend/Handle.h`, `backend/DriverEnums.h` - 后端类型
- `math/vec4.h` - 数学类型
