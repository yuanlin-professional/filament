# WebGPUStrings

## 文件概述

WebGPU 后端的字符串工具头文件（纯头文件），提供大量 `constexpr` 和 `inline` 函数将 WebGPU 和 Filament 枚举值转换为可读字符串，主要用于调试日志输出。

## 核心函数

- `errorTypeToString` / `powerPreferenceToString` / `backendTypeToString` / `adapterTypeToString` - WebGPU 系统枚举转字符串
- `deviceLostReasonToString` - 设备丢失原因
- `webGPUTextureFormatToString` / `webGPUTextureDimensionToString` - 纹理格式和维度
- `filamentShaderStageToString` - Filament 着色器阶段
- `filamentDescriptorTypeToString` / `filamentStageFlagsToString` / `filamentDescriptorFlagsToString` - 描述符相关
- `filamentSampler*ToString` - 采样器参数
- `filamentTextureUsageFlagsToString` - 纹理用途标志组合
- `webGPUTextureToString` - 完整纹理信息字符串

## 关键实现逻辑

- 大部分函数使用 `constexpr` 在编译时求值
- 条件编译：许多函数仅在特定调试标志启用时编译（`FWGPU_PRINT_SYSTEM`、`FWGPU_DEBUG_BIND_GROUPS` 等）

## 依赖关系

- `WebGPUConstants.h` - 调试标志宏
- `dawn/webgpu_cpp_print.h` - Dawn 的 WebGPU 打印支持
- `backend/DriverEnums.h` - Filament 枚举
