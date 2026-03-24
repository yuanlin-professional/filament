# surface_shading_model_standard.fs

## 文件概述
标准 PBR 着色模型实现。实现了 Filament 的标准 Lit 着色模型，包含漫反射项、镜面反射项、透明涂层和各向异性等完整的物理光照计算。

## 核心功能
- 标准 GGX 镜面反射（D_GGX * V_SmithGGX * F_Schlick）
- Lambert 漫反射
- 可选透明涂层层（Clear Coat）
- 可选各向异性镜面反射
- 能量补偿多散射项

## 依赖关系
- `surface_brdf.fs` - BRDF 函数
- `surface_lighting.fs` - Light / PixelParams 结构体
