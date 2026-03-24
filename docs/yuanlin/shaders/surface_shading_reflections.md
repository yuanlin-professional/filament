# surface_shading_reflections.fs

## 文件概述
屏幕空间反射（SSR）着色处理。将屏幕空间反射数据与间接光照结果混合，实现动态反射效果。

## 核心功能
- 处理 SSR 变体激活时的反射数据混合
- 将反射颜色与间接镜面反射合并

## 依赖关系
- `surface_lighting.fs` - PixelParams
- 需要 `VARIANT_HAS_SSR` 变体标志
