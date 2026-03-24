# WebGPUPipelineLayoutCache

## 文件概述

`WebGPUPipelineLayoutCache` 负责创建和缓存 `wgpu::PipelineLayout` 对象，与 `WebGPUPipelineCache` 配合使用。

## 核心类/结构体

- **`WebGPUPipelineLayoutCache`**
  - **`PipelineLayoutRequest`** - 创建请求：标签、绑定组布局数组及数量
  - **`PipelineLayoutKey`** (40 字节) - 缓存键，存储 `WGPUBindGroupLayout` 原始句柄指针数组
  - **`PipelineLayoutCacheEntry`** - 缓存条目，包含布局和最后使用帧数

## 关键实现逻辑

1. **键使用原始句柄**: 使用 `WGPUBindGroupLayout` 原始指针而非包装类，减小键的内存占用
2. **LRU 过期**: 超过 90 帧未使用的管线布局被移除
3. **逐字节比较**: `memcmp` 比较 POD 键结构

## 依赖关系

- `WebGPUConstants.h` - 过期帧数常量、systrace 宏
- `backend/DriverEnums.h` - `MAX_DESCRIPTOR_SET_COUNT`
- `tsl/robin_map.h` / `utils/Hash.h`
