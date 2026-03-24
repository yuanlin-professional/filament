# MetalUtils

## 文件概述

MetalUtils 提供了 Metal 纹理视图创建和 uniform buffer 对齐等底层工具函数。这些函数被 MetalBlitter、MetalHandles 等多个模块共同使用。

**源文件**: `MetalUtils.h`, `MetalUtils.mm`

## 核心类/结构体

无类定义，全部为工具函数。

## 关键实现逻辑

- **`createTextureViewWithSwizzle(texture, swizzle)`** (iOS 13+) - 创建带通道重映射的纹理视图。如果 swizzle 为默认值（RGBA -> RGBA），直接返回原纹理避免不必要的视图创建。支持 Cube/CubeArray 纹理的 slice 计算（arrayLength * 6）
- **`createTextureViewWithLodRange(texture, lodMin, lodMax)`** - 创建指定 mip level 范围的纹理视图。如果范围覆盖所有 mip level，直接返回原纹理。用于纹理视图（texture view）的 LOD 限制
- **`createTextureViewWithSingleSlice(texture, slice)`** - 创建纹理数组/立方体纹理中单个 slice 的 2D 视图。2D 纹理直接返回。用于 blit 操作中按层处理纹理数组
- **`getUniformBufferOffsetAlignment()`** - 返回 uniform buffer 偏移对齐要求：
  - iOS 模拟器: 256 字节
  - iOS 真机: 4 字节
  - macOS: 32 字节

## 依赖关系

- `Metal/Metal.h` - MTLTexture、MTLTextureSwizzleChannels
- `utils/debug.h` - assert_invariant
