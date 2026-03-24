# surface_shading_model_subsurface.fs

## 文件概述
次表面散射着色模型实现。在标准 PBR 基础上增加了次表面光传输效果，模拟光线穿透薄物体（如皮肤、叶片、蜡烛）后从背面散射出的光照。

## 核心功能
- 在标准 GGX 镜面反射和 Lambert 漫反射之上增加次表面散射项
- 次表面散射颜色和厚度参数控制透光效果
- 考虑了光源方向与表面法线的关系来计算背面透射

## 依赖关系
- `surface_brdf.fs` - BRDF 函数
- `surface_shading_model_standard.fs` - 标准着色模型基础
