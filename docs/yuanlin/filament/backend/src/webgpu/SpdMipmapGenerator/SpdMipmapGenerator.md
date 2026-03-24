# SpdMipmapGenerator

## 文件概述

SPD (Single Pass Downsampler) Mipmap 生成器，基于 AMD FidelityFX SPD 算法的 WebGPU/WGSL 移植。使用计算着色器在尽可能少的通道中生成纹理 mipmap，利用工作组共享内存和原子操作实现高效降采样。

## 核心类/结构体

- **`spd::MipmapGenerator`** - mipmap 生成器主类
  - `m_maxMipsPerPass` - 单次通道可生成的最大 mip 级别数（取决于设备 `maxStorageTexturesPerShaderStage`）
  - `m_pipelines` - 管线缓存
- **`SPDPassConfig`** - 通道配置（过滤器、目标纹理、mip 数、半精度标志等）
- **`SPDPipeline`** - 管线及其绑定组布局
- **`SPDFilter`** - 降采样滤波器枚举：Average、Min、Max、MinMax
- **`SPDScalarType`** - 着色器标量类型：F32、F16、I32、U32

## 关键实现逻辑

1. **动态着色器生成**: `MakeShaderCode` 根据输出格式、滤波器、mip 数和标量类型动态生成 WGSL 计算着色器
2. **分块处理**: 以 64x64 像素块为单位调度工作组，每个工作组 256 线程
3. **两阶段管线**: 当 mip 数 <= 6 时使用基本管线（仅 uniform buffer）；> 6 时使用高级管线（增加原子计数器和中间 mip 缓冲区）
4. **多通道处理**: 若总 mip 数超过单次通道上限，自动拆分为多次通道
5. **数组层分块**: 对于纹理数组，按 `maxStorageTexturesPerShaderStage` 分块处理

## 依赖关系

- `webgpu/webgpu_cpp.h` - WebGPU C++ API
- `tsl/robin_map.h` - 管线缓存
