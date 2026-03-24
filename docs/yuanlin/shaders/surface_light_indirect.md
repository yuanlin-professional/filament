# surface_light_indirect.fs

## 文件概述
间接光照（IBL）评估。从环境贴图和 DFG LUT 计算间接漫反射和间接镜面反射，支持球谐辐照度和预过滤环境贴图。

## 核心功能
- 间接漫反射：使用球谐函数或辐照度贴图
- 间接镜面反射：根据粗糙度采样预过滤环境贴图 mipmap
- DFG 预积分查找表应用
- 能量补偿多散射项
- 可选环境光遮蔽（AO）调制

## 依赖关系
- `surface_ambient_occlusion.fs` - AO 计算
- `surface_lighting.fs` - PixelParams
