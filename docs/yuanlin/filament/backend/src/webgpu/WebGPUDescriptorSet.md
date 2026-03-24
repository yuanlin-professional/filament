# WebGPUDescriptorSet

## 文件概述

`WebGPUDescriptorSet` 是 `HwDescriptorSet` 的 WebGPU 实现，管理一组资源绑定（纹理、缓冲区、采样器），对应 WebGPU 的 `wgpu::BindGroup`。采用延迟创建模式：先添加绑定项，最后通过 `lockAndReturn` 一次性创建 BindGroup。

## 核心类/结构体

- **`WebGPUDescriptorSet`** - 描述符集
  - `mLayout` - 对应的 `wgpu::BindGroupLayout`
  - `mEntries` - 绑定项列表
  - `mEntryIndexByBinding` - binding index 到内部数组索引的映射表
  - `mBindGroup` - 延迟创建的 `wgpu::BindGroup`

## 关键实现逻辑

1. **索引映射**: 构造时预分配 `mEntries` 并建立 binding -> entry 的快速查找映射
2. **锁定语义**: `lockAndReturn` 创建 BindGroup 后释放布局和绑定项数组（`shrink_to_fit`），降低内存占用
3. **幂等保护**: 重复调用 `addEntry`（在已锁定状态下）或 `lockAndReturn` 均安全返回

## 依赖关系

- `WebGPUDescriptorSetLayout.h` - 布局定义（`BindGroupEntryInfo`）
- `WebGPUConstants.h` - `MAX_DESCRIPTOR_COUNT`
- `backend/DriverEnums.h` - Filament 驱动枚举
