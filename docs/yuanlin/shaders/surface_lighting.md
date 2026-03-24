# surface_lighting.fs

## 文件概述
表面光照核心结构体定义。声明了 `Light`（光源参数）和 `PixelParams`（像素着色参数）两个关键数据结构，是所有光照计算的基础。

## 核心结构体
- `Light` - 光源信息：颜色强度、方向、衰减、世界位置、NoL、阴影标志、光源类型、通道等
- `PixelParams` - 像素级着色参数：
  - 漫反射颜色、感知粗糙度、f0（菲涅尔反射率）
  - DFG 预积分项、能量补偿项
  - 可选：透明涂层参数、各向异性参数、次表面散射参数
  - 可选：光泽度（sheen）、折射（refraction）参数

## 依赖关系
- 被 `surface_light_directional.fs`、`surface_light_punctual.fs`、`surface_light_indirect.fs` 等使用
