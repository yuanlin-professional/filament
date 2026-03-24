# WebGPUTexture

## 文件概述

`WebGPUTexture` 是 `HwTexture` 的 WebGPU 实现，管理 GPU 纹理及其默认视图。支持多种纹理类型（2D、2D Array、CubeMap、3D）、swizzle 视图和 MSAA sidecar 纹理。

## 核心类/结构体

- **`WebGPUTexture`** (继承 `HwTexture`)
  - `MipmapGenerationStrategy` - mipmap 生成策略枚举（`RENDER_PASS`、`SPD_COMPUTE_PASS`、`NONE`）
  - `mTexture` / `mDefaultTextureView` - 底层纹理和默认视图
  - `mMsaaSidecarTexture` - MSAA 侧车纹理
  - `mSwizzle` - 纹理通道重排信息

## 关键实现逻辑

1. **三种构造方式**: 全新纹理、基于源纹理的 mip 级别子视图、基于源纹理的 swizzle 视图
2. **格式策略**: 若需 SPD 计算通道生成 mipmap，底层格式使用 storage-compatible 格式（如 RGBA8UnormSrgb -> RGBA8Unorm）
3. **用途映射**: `fToWGPUTextureUsage` 将 Filament 纹理用途映射为 WebGPU 用途，考虑 transient attachment 优化
4. **MSAA Sidecar**: 为单采样纹理创建多采样侧车纹理，用于渲染目标的 MSAA
5. **swizzle 组合**: `composeSwizzle` 将前后两次 swizzle 操作合并为一个

## 依赖关系

- `WebGPUTextureHelpers.h` - 格式转换、用途映射等大量辅助函数
- `WebGPURenderPassMipmapGenerator.h` - 格式兼容性检查
- `WebGPUConstants.h` / `WebGPUStrings.h` - 调试输出
