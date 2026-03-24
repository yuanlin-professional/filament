# common_graphics.fs

## 文件概述
通用颜色操作函数集（片段着色器）。提供亮度计算、预曝光强度、YCbCr 到 sRGB 转换、alpha 预乘等基础图形函数。

## 核心函数
- `luminance()` - 使用 Rec.709 系数计算线性 RGB 的亮度值
- `computePreExposedIntensity()` - 计算预曝光强度（强制 highp 精度）
- `unpremultiply()` - 反预乘 alpha
- YCbCr 到 sRGB 转换函数（公开 API）

## 依赖关系
- `common_math.glsl` - `FLT_EPS` 常量
