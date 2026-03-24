# Enums.cpp

## 文件概述

定义了 `filamat::Enums` 类的静态成员变量，建立字符串到枚举值的映射表。这些映射表用于材质文件解析（如 `.mat` 文件），将文本形式的属性名或类型名转换为对应的 C++ 枚举值。

## 核心映射表

### `mStringToProperty` -- 材质属性映射
将 `"baseColor"`、`"roughness"`、`"metallic"` 等 30 个 PBR 材质属性名映射到 `Property` 枚举值，涵盖标准 PBR（金属度/粗糙度）、清漆（clearCoat）、各向异性、次表面散射、光泽、折射、色散等全部材质属性。

### `mStringToUniformType` -- Uniform 类型映射
将 `"bool"`、`"float"`、`"float2"`~`"float4"`、`"int"`~`"int4"`、`"uint"`~`"uint4"`、`"mat3"`/`"mat4"` 等映射到 `UniformType` 枚举。其中 `"float3x3"` 和 `"float4x4"` 是 `"mat3"` 和 `"mat4"` 的别名。

### `mStringToSamplerType` -- 采样器类型映射
将 `"sampler2d"`、`"sampler2dArray"`、`"sampler3d"`、`"samplerCubemap"`、`"samplerExternal"`、`"samplerCubemapArray"` 映射到 `SamplerType` 枚举。

### 其他映射表
- **`mStringToSubpassType`**: 子通道类型（`"subpassInput"`）
- **`mStringToSamplerPrecision`**: 精度（`"default"`/`"low"`/`"medium"`/`"high"`）
- **`mStringToOutputTarget`**: 输出目标（`"color"`/`"depth"`）
- **`mStringToOutputQualifier`**: 输出限定符（`"out"`）
- **`mStringToOutputType`**: 输出类型（`"float"`~`"float4"`）
- **`mStringToSamplerFormat`**: 采样器格式（`"int"`/`"uint"`/`"float"`/`"shadow"`）
- **`mStringToConstantType`**: 常量类型（`"int"`/`"float"`/`"bool"`）
- **`mStringToShaderStageType`**: 着色器阶段（`"fragment"`/`"vertex"`/`"compute"`）

## 关键实现逻辑

每个映射表通过 `template<> getMap<EnumType>()` 模板特化方法暴露给外部使用。`Enums` 类提供统一的字符串到枚举的查询接口，支持材质文件的文本解析流程。

## 依赖关系

- `"filamat/Enums.h"` -- `Enums` 类声明
- `"filamat/MaterialBuilder.h"` -- 枚举类型定义
- `<string_view>` / `<unordered_map>` -- 标准库
