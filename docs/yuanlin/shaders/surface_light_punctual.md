# surface_light_punctual.fs

## 文件概述
点光源和聚光灯光照评估。遍历影响当前片段的所有局部光源，计算衰减、阴影和光照贡献。

## 核心功能
- 点光源距离衰减（平滑衰减曲线）
- 聚光灯角度衰减
- 局部光源阴影贴图采样
- 光照通道过滤

## 依赖关系
- `surface_shadowing.fs` - 阴影计算
- `surface_shading_lit.fs` - 着色模型调用
- `surface_lighting.fs` - Light 结构体
