# MetalEnums

## 文件概述

MetalEnums 提供了 Filament 引擎枚举类型到 Metal API 枚举类型的全面转换函数。涵盖深度比较函数、模板操作、顶点格式、像素格式、混合状态、采样器参数、纹理类型等所有需要映射的枚举。

**源文件**: `MetalEnums.h`, `MetalEnums.mm`

## 核心类/结构体

本文件无定义类，全部为转换函数。

## 关键实现逻辑

- **顶点格式转换**: `getMetalFormat(ElementType, bool normalized)` 将 Filament 的元素类型映射到 `MTLVertexFormat`，支持 normalized 和非 normalized 两种模式
- **纹理格式转换**: `getMetalFormat(MetalContext*, TextureFormat)` 是最复杂的转换，需要根据设备 GPU 系列和 OS 版本动态判断支持情况：
  - 基础格式（8-128 位）直接映射
  - 16 位 packed 格式需要 Apple GPU 系列 >= 1 且 macOS 11+
  - ASTC 纹理在 Apple GPU >= 2 上使用 HDR profile，否则回退到 LDR
  - EAC/ETC2 压缩格式仅 Apple GPU 支持
  - DXT/BC 压缩格式仅 macOS 桌面支持
  - 24 位 RGB 格式 Metal 不原生支持，返回 `MTLPixelFormatInvalid`
- **像素数据格式转换**: `getMetalFormat(PixelDataFormat, PixelDataType)` 用于纹理上传时的格式匹配
- **sRGB 线性化**: `getMetalFormatLinear()` 将 sRGB 格式映射到对应的线性格式
- **整数格式判断**: `isMetalFormatUnsignedInteger/SignedInteger/Integer` 用于 blit 时选择正确的着色器变体
- **采样器映射**: 过滤模式、包裹模式、比较函数的完整映射
- **纹理 Swizzle**: `getSwizzle/getSwizzleChannels` 用于纹理通道重映射（iOS 13+）

## 依赖关系

- `private/backend/Driver.h` - Filament 枚举定义
- `MetalContext.h` - 访问 GPU 系列信息
- `Metal/Metal.h` - Metal 枚举类型
