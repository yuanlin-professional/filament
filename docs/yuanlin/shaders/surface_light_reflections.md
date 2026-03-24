# surface_light_reflections.fs

## 文件概述
反射光照处理。管理屏幕空间反射（SSR）和其他反射技术的集成，将反射结果合并到最终光照中。

## 核心功能
- SSR 数据的获取和应用
- 反射贡献与间接光照的混合

## 依赖关系
- `surface_lighting.fs` - PixelParams
- `surface_light_indirect.fs` - 间接光照基础
