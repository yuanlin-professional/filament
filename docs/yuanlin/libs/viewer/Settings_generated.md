# Settings_generated.h / Settings_generated.cpp

## 文件概述
自动生成的代码，为 Filament View 选项结构体（如 AmbientOcclusionOptions、BloomOptions 等）提供 JSON 序列化/反序列化和流输出支持。

## 核心功能
- 为 `filament::View` 的各种选项结构体提供 `parse` 函数（JSON 反序列化）
- 为各种枚举类型提供 `parse` 和 `operator<<` 重载
- 涵盖的结构体包括：AmbientOcclusionOptions、BloomOptions、DepthOfFieldOptions、FogOptions、VignetteOptions、DynamicResolutionOptions、MultiSampleAntiAliasingOptions、TemporalAntiAliasingOptions 等

## 依赖关系
- `filament/View.h` - 选项结构体定义
- `jsonParseUtils.h` - JSON 解析基础设施
