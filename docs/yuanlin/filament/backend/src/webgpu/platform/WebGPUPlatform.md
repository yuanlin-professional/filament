# WebGPUPlatform

## 文件概述

`WebGPUPlatform` 的跨平台核心实现，处理 WebGPU 实例创建、适配器选择和设备请求。此文件包含所有平台共享的逻辑。

## 核心逻辑

### 实例创建 (`createInstance`)
- 启用 `TimedWaitAny` 实例特性
- 可选启用 Dawn 的 `enable_immediate_error_handling` toggle

### 适配器选择 (`requestAdapter` -> `selectPreferredAdapter`)
1. 并行请求多种配置的适配器（不同电源偏好、后端类型、是否回退）
2. 验证必需特性: `TransientAttachments`、`RG11B10UfloatRenderable`、`Float32Filterable`
3. 验证必需限制: maxBindGroups、maxVertexBuffers、maxVertexAttributes 等
4. 选择策略: 优先高性能 -> 更多可选特性

### 设备请求 (`requestDevice`)
- 启用所有必需和已支持的可选特性
- 请求 `maxStorageTexturesPerShaderStage` 最大到 12（用于 SPD mipmap）
- 注册设备丢失和未捕获错误回调

### 可选特性
- `CoreFeaturesAndLimits`、`DepthClipControl`、`Depth32FloatStencil8`、`TextureComponentSwizzle`

## 依赖关系

- `WebGPUDriver.h` - 驱动创建
- `WebGPUConstants.h` / `WebGPUStrings.h` - 常量和调试输出
- `backend/platforms/WebGPUPlatform.h` - 平台抽象基类
