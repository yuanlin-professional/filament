# WebGPURenderPassMipmapGenerator

## 文件概述

`WebGPURenderPassMipmapGenerator` 使用一系列渲染通道为纹理生成 mipmap。每个 mip 级别通过一个全屏三角形渲染通道从前一级采样生成。

## 核心类/结构体

- **`WebGPURenderPassMipmapGenerator`**
  - **`FormatCompatibility`** - 格式兼容性信息（是否支持 + 原因）
  - `mPreviousMipLevelSampler` - 线性采样器
  - `mShaderModule` - 顶点+片段着色器模块
  - `mPipelineByTextureFormat` - 按纹理格式缓存的渲染管线

## 关键实现逻辑

1. **兼容性检查**: `getCompatibilityFor` 验证格式（仅 f32 采样类型）、维度（仅 2D）和采样数（仅单采样）
2. **全屏三角形绘制**: WGSL 着色器中硬编码 3 顶点的全屏三角形，使用 `textureSample` 从前一 mip 级别采样
3. **逐层逐级生成**: 对每个数组层的每个 mip 级别执行独立的渲染通道
4. **管线缓存**: 按纹理格式缓存渲染管线，复用采样器、着色器和绑定组布局

## 依赖关系

- `WebGPUQueueManager.h` - 获取命令编码器和刷新队列
- `tsl/robin_map.h` - 管线缓存
