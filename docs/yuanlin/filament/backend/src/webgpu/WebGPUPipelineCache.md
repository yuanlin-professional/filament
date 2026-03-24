# WebGPUPipelineCache

## 文件概述

`WebGPUPipelineCache` 负责创建和缓存 `wgpu::RenderPipeline` 对象，避免在运行时进行高开销的管线创建。

## 核心类/结构体

- **`WebGPUPipelineCache`**
  - **`RenderPipelineRequest`** - 管线创建请求，包含着色器、顶点布局、管线布局、光栅化状态、模板/深度状态等
  - **`RenderPipelineKey`** (360 字节) - 管线缓存键，紧凑的 POD 结构体用于高效 MurmurHash
  - **`RenderPipelineCacheEntry`** - 缓存条目，包含管线和最后使用帧数

## 关键实现逻辑

1. **键设计**: `RenderPipelineKey` 使用最小内存表示（单字节枚举、原始句柄指针），确保可逐字节比较和高效哈希
2. **Filament 到 WebGPU 转换**: 提供一系列 `toWebGPU` 函数将 Filament 枚举转为 WebGPU 对应项（拓扑、剔除模式、混合因子等）
3. **LRU 过期机制**: `onFrameEnd` 每帧递增帧计数器并移除超过 45 帧未使用的管线
4. **无片段着色器管线**: 深度-only 通道可以创建没有 fragment stage 的管线

## 依赖关系

- `WebGPUVertexBufferInfo.h` - 顶点缓冲区槽绑定信息
- `WebGPUTextureHelpers.h` - 深度/模板格式检查
- `tsl/robin_map.h` - 高性能哈希映射
- `utils/Hash.h` - MurmurHash
