# surface_ambient_occlusion.fs

## 文件概述
环境光遮蔽（AO）处理。综合材质 AO、SSAO（屏幕空间环境光遮蔽）和多弹跳 AO 近似，计算最终的环境光遮蔽因子。

## 核心功能
- 组合材质定义的 AO 值和 SSAO 纹理采样结果
- 镜面反射 AO 近似（基于 Lagarde 的方法）
- 多弹跳 AO 颜色修正

## 依赖关系
- `surface_lighting.fs` - PixelParams
- SSAO 纹理采样器
