# WebGPUMsaaTextureResolver

## 文件概述

`WebGPUMsaaTextureResolver` 用于在 WebGPU 中将多采样纹理解析为单采样纹理。WebGPU 不像其他图形 API 那样具有隐式的解析步骤，因此需要显式执行。

## 核心类/结构体

- **`WebGPUMsaaTextureResolver`**
  - **`ResolveRequest`** - 解析请求参数
    - **`TextureInfo`** - 包含纹理引用、视图维度、mip 级别、层级、aspect
    - `commandEncoder` - 命令编码器
    - `viewFormat` - 视图格式

## 关键实现逻辑

1. **颜色纹理解析**: 通过创建渲染通道，将多采样纹理作为颜色附件的 `view`，单采样纹理作为 `resolveTarget`，利用 WebGPU 的隐式通道解析功能
2. **前置条件校验**: 验证源/目标尺寸匹配、采样数正确（源 > 1，目标 = 1）、格式一致、不支持深度/模板格式
3. **深度解析**: 目前未实现，会触发 panic

## 依赖关系

- `WebGPUTextureHelpers.h` - `hasDepth`、`hasStencil` 等辅助函数
- `utils/Panic.h` - 断言和错误处理
