# surface_shading_unlit.fs

## 文件概述
Unlit（无光照）着色模型实现。直接使用材质的发光色（emissive）作为输出颜色，不进行任何光照计算，用于 UI 元素、特效粒子等不需要光照响应的物体。

## 核心功能
- 直接输出材质发光色
- 可选后光照颜色混合

## 依赖关系
- `surface_material.fs` - 获取材质发光色
