# common_shading.fs

## 文件概述
着色通用变量声明。定义了片段着色器中着色计算所需的全局变量，包括 TBN 矩阵、片段位置、视线方向、法线、反射方向等。

## 核心变量
- `shading_tangentToWorld` - TBN（切线-副切线-法线）矩阵
- `shading_position` - 片段世界空间位置
- `shading_view` - 归一化的片段到相机方向
- `shading_normal` / `shading_geometricNormal` - 变换后的法线 / 几何法线
- `shading_reflected` - 视线关于法线的反射方向
- `shading_NoV` - dot(normal, view)，保证 >= MIN_N_DOT_V
- `shading_bentNormal` - 弯曲法线（可选）
- `shading_clearCoatNormal` - 透明涂层法线（可选）
- `shading_normalizedViewportCoord` - 归一化视口坐标

## 依赖关系
- 由 `surface_shading_parameters.fs` 填充，被各光照函数使用
