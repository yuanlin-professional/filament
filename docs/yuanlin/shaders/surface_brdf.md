# surface_brdf.fs

## 文件概述
BRDF（双向反射分布函数）配置和实现。定义了漫反射和镜面反射的各种 BRDF 模型选项，并根据质量等级选择默认配置。

## 核心定义
- 漫反射模型：`DIFFUSE_LAMBERT` (0)、`DIFFUSE_BURLEY` (1)
- 法线分布函数（NDF）：`SPECULAR_D_GGX` (0)、`SPECULAR_D_GGX_ANISOTROPIC` (0)、`SPECULAR_D_CHARLIE` (0, 布料)
- 可见性函数：`SPECULAR_V_SMITH_GGX` (0)、`SPECULAR_V_SMITH_GGX_FAST` (1)、`SPECULAR_V_KELEMEN` (3)、`SPECULAR_V_NEUBELT` (4)
- Fresnel 函数：`SPECULAR_F_SCHLICK` (0)

## 关键实现逻辑
- 高质量模式使用 `SMITH_GGX` 可见性函数，低质量使用 `SMITH_GGX_FAST`
- 默认漫反射为 Lambert 模型
- 包含 GGX NDF、Smith 可见性、Schlick Fresnel 等标准 PBR BRDF 函数实现

## 依赖关系
- `common_math.glsl` - 数学工具
- 被 `surface_shading_model_standard.fs` 等使用
