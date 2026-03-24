# WebGPUVertexBufferInfo

## 文件概述

`WebGPUVertexBufferInfo` 是 `HwVertexBufferInfo` 的 WebGPU 实现，负责将 Filament 顶点属性映射为 WebGPU 的顶点缓冲区布局。

## 核心类/结构体

- **`WebGPUVertexBufferInfo`** (继承 `HwVertexBufferInfo`)
  - `mVertexBufferLayouts` - WebGPU 顶点缓冲区布局数组
  - `mVertexAttributes` - 所有顶点属性的扁平数组
  - `mWebGPUSlotBindingInfos` - 每个 WebGPU 槽的绑定信息
  - **`WebGPUSlotBindingInfo`** - 源缓冲区索引、偏移、步幅

## 关键实现逻辑

1. **交错属性分组**: 通过比较缓冲区索引、步幅和偏移范围，将交错的属性分入同一 WebGPU 槽/布局
2. **属性排序**: 按槽索引（再按偏移）排序，确保同一布局的属性在数组中连续
3. **格式映射**: `getVertexFormat` 将 Filament `ElementType` + 标志映射为 `wgpu::VertexFormat`，处理 WebGPU 不支持的 x3 字节/半精度格式（回退到 x4）
4. **设备限制校验**: 检查属性数和缓冲区数不超过设备和 Filament 的最大值

## 依赖关系

- `WebGPUConstants.h` - 日志宏
- `DriverBase.h` / `backend/DriverEnums.h` - 属性类型定义
