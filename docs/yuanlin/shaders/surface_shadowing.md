# surface_shadowing.fs / surface_shadowing.glsl

## 文件概述
阴影计算模块（片段着色器 + 通用 GLSL）。实现 PCF（百分比接近过滤）、VSM（方差阴影贴图）和接触阴影等多种阴影技术。

## 核心功能
- PCF 阴影过滤：多种核大小（2x2 到更大）
- VSM 阴影：基于方差的软阴影
- 级联阴影贴图选择和过渡
- 接触阴影（屏幕空间光线步进）
- 法线偏移和深度偏移处理

## 依赖关系
- `surface_types.glsl` - ShadowData 结构体
- 阴影贴图采样器
