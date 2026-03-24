# WebGPUBlitter

## 文件概述

`WebGPUBlitter` 负责在 WebGPU 后端中执行纹理间的 blit（像素拷贝/转换）操作。blit 操作可以是简单的逐字节拷贝，也可以涉及像素格式转换、图像缩放、滤波（如下采样）或多重采样解析等变换。该类在其所有者（WebGPU 驱动）的生命周期内缓存管线状态以优化后续调用。

## 核心类/结构体

- **`WebGPUBlitter`** - 主类，管理 blit 操作的全部流程
  - **`BlitArgs`** - blit 参数封装，包含源/目标纹理的 `Attachment` 信息及过滤模式
  - **`BlitArgs::Attachment`** - 描述单个纹理附件：纹理引用、aspect、origin、extent、mipLevel、layerOrDepth
  - **`RenderPipelineKey`** (20 字节) - 渲染管线缓存键，包含源纹理维度、格式、采样类型、目标格式、采样数、过滤类型
  - **`PipelineLayoutKey`** (12 字节) - 管线布局缓存键
  - **`ShaderModuleKey`** (16 字节) - 着色器模块缓存键

## 关键实现逻辑

1. **直接拷贝判定** (`canDoDirectCopy`): 当源和目标纹理格式兼容、尺寸相同、采样数一致时，使用 `CopyTextureToTexture` 进行高效逐字节拷贝
2. **渲染通道 Blit**: 不满足直接拷贝条件时，通过自定义 WGSL 着色器执行全屏三角形渲染，实现格式转换、缩放和 MSAA 解析
3. **多级缓存模式**: 使用 `getOrCreate` 模式缓存 `RenderPipeline`、`PipelineLayout`、`BindGroupLayout`、`ShaderModule`，避免重复创建
4. **WGSL 着色器模板**: 着色器源码通过模板占位符机制动态生成，支持 2D/3D/多采样输入纹理及整数/浮点/深度格式

## 依赖关系

- `WebGPUConstants.h` - 调试标志和常量
- `WebGPUTextureHelpers.h` - 纹理格式辅助函数（`hasDepth`、`getBlockSize`、`areCopyCompatible` 等）
- `webgpu/utils/StringPlaceholderTemplateProcessor.h` - 着色器模板占位符替换
- `backend/DriverEnums.h` - Filament 驱动枚举
- `utils/Hash.h` - MurmurHash 用于缓存键哈希
- `tsl/robin_map.h` - 高性能哈希映射
