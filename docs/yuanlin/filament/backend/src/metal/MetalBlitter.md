# MetalBlitter

## 文件概述

MetalBlitter 负责在 Metal 后端执行纹理 blit（位块传输）操作。支持在不同格式、不同采样数的纹理之间进行复制、缩放和 MSAA resolve。当源和目标纹理的格式/采样数/尺寸完全一致时，使用 Metal 原生的 `MTLBlitCommandEncoder`（快速路径）；否则回退到基于渲染管线的着色器 blit（慢速路径）。

**源文件**: `MetalBlitter.h`, `MetalBlitter.mm`

## 核心类/结构体

- **`MetalBlitter`** - 主类，管理 blit 操作的执行
  - `BlitArgs` - blit 参数结构体，包含源和目标的 `Attachment`（纹理、区域、mip level、slice）
  - `BlitArgs::Attachment` - 描述单个 blit 端点（纹理、区域、层级、切片）
  - `BlitFunctionKey` - 用于缓存片段着色器函数的键，区分 MSAA/3D 源和 float/uint/int 数据格式

## 关键实现逻辑

- **双路径策略**: `blit()` 先尝试快速路径 `blitFastPath()`，在格式/采样数/尺寸完全匹配时使用 `MTLBlitCommandEncoder::copyFromTexture`；失败则回退到 `blitSlowPath()`
- **着色器 blit（慢速路径）**: 通过渲染一个覆盖整个 viewport 的三角形（3 顶点，坐标为 -1,-1 / -1,3 / 3,-1），在片段着色器中采样源纹理完成 blit
- **着色器缓存**: 使用 `tsl::robin_map` 缓存编译好的片段着色器函数（`mBlitFunctions`），键为 `BlitFunctionKey`，通过预处理器宏（`MSAA_COLOR_SOURCE`、`SOURCES_3D`、`INPUT_FORMAT`、`OUTPUT_FORMAT`）生成不同变体
- **内联 MSL 代码**: 片段着色器源码以 C++ 字符串常量内嵌，运行时通过 `newLibraryWithSource` 动态编译

## 依赖关系

- `MetalContext.h` - 访问设备、管线状态缓存、采样器状态缓存
- `MetalEnums.h` - 判断像素格式是否为整数类型
- `MetalUtils.h` - 创建纹理视图（单 slice）
- `backend/DriverEnums.h` - 采样器过滤模式
- `tsl/robin_map.h` - 哈希表缓存
