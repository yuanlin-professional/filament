# common_defines.glsl

## 文件概述
GLSL 通用类型定义文件。定义了 HLSL 风格的类型别名（如 `float2`、`int3`、`bool4`），以及 Vulkan 语义布局位置宏和特定 GPU 驱动的 workaround 宏。

## 核心定义
- `LAYOUT_LOCATION(x)` - Vulkan 环境下展开为 `layout(location = x)`，否则为空
- `float2`/`float3`/`float4` - vec2/vec3/vec4 的别名
- `int2`/`uint2` 等 - ivec/uvec 的别名
- `float3x3`/`float4x4` - mat3/mat4 的别名
- `highp_mat4` - Vulkan+移动端使用非 const mat4（Adreno 崩溃 workaround）

## 依赖关系
- 被所有着色器文件隐式包含
