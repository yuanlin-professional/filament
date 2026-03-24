# WebGPUDescriptorSetLayout

## 文件概述

`WebGPUDescriptorSetLayout` 是 `HwDescriptorSetLayout` 的 WebGPU 实现，定义描述符集的布局结构，即管线可绑定资源的类型和排列方式。

## 核心类/结构体

- **`WebGPUDescriptorSetLayout`** - 描述符集布局
  - `mLayout` - 底层 `wgpu::BindGroupLayout`
  - `mBindGroupEntries` - 绑定组条目信息列表（binding 索引和动态偏移标志）
  - **`BindGroupEntryInfo`** - 简化的绑定条目信息：`binding` 和 `hasDynamicOffset`

## 关键实现逻辑

1. **binding 索引双倍映射**: WebGPU 中纹理和采样器是分离的绑定。Filament 的 binding index 被映射为：
   - 纹理: `binding * 2`
   - 采样器: `binding * 2 + 1`
2. **采样器类型推断**: 根据描述符类型自动选择 `Comparison`（深度）、`NonFiltering`（整数/不可过滤）或 `Filtering`
3. **ShaderStage 转换**: `filamentStageToWGPUStage` 将 Filament 着色器阶段标志转换为 WebGPU 着色器可见性

## 依赖关系

- `DriverBase.h` / `backend/DriverEnums.h` - Filament 驱动基础设施
- `utils/BitmaskEnum.h` - 位掩码枚举操作
