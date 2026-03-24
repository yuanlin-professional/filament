# WebGPUTextureHelpers

## 文件概述

纹理相关的辅助函数集合（纯头文件），提供格式查询、格式转换、用途映射、swizzle 组合等大量 `constexpr` 工具函数，被 WebGPU 后端各处广泛使用。

## 核心函数分类

### 格式查询
- `hasDepth` / `hasStencil` / `isUIntFormat` / `isIntFormat` - 格式特征判断
- `getBlockSize` - 获取压缩格式的块大小
- `getWGPUTextureFormatPixelSize` - 非压缩格式的像素字节大小
- `isFormatStorageCompatible` - 是否支持 StorageBinding
- `areCopyCompatible` - 两个格式是否 copy-compatible

### 格式转换
- `toWGPUTextureFormat` - Filament TextureFormat -> wgpu::TextureFormat
- `toWebGPUFormat` - PixelDataFormat + PixelDataType -> wgpu::TextureFormat
- `toLinearFormat` - sRGB 格式转线性格式

### 维度/用途转换
- `toWebGPUTextureViewDimension` / `toWebGPUTextureDimension` - SamplerType -> WebGPU 维度
- `fToWGPUTextureUsage` - Filament TextureUsage -> wgpu::TextureUsage
- `toWGPUComponentSwizzle` - Filament TextureSwizzle -> wgpu::ComponentSwizzle

### Swizzle
- `composeSwizzle` - 组合两个 swizzle 操作

## 依赖关系

- `WebGPUConstants.h` - 日志宏
- `backend/DriverEnums.h` - Filament 枚举定义
