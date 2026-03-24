# surface_light_directional.fs

## 文件概述
方向光（太阳光）光照评估。计算主方向光源对表面的直接光照贡献，包括阴影采样和级联阴影贴图选择。

## 核心功能
- 方向光的漫反射和镜面反射贡献计算
- 级联阴影贴图采样和过滤
- 支持 VSM（方差阴影贴图）

## 依赖关系
- `surface_shadowing.fs` - 阴影计算
- `surface_shading_lit.fs` - 着色模型调用
