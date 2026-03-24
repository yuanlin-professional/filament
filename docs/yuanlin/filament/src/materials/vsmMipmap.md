# vsmMipmap

## 文件概述

`vsmMipmap.mat` 定义了 VSM（Variance Shadow Map）专用的 mipmap 生成材质，使用自定义的 box 滤波器替代 GPU 默认的双线性插值。

## 材质属性

- **域**: `postprocess`
- **深度写入/裁剪**: 均禁用
- **裁剪**: 无

## 参数

| 名称 | 类型 | 说明 |
|------|------|------|
| `color` | sampler2dArray | VSM 阴影贴图数组纹理 |
| `layer` | int | 层索引 |
| `uvscale` | float | UV 缩放 |

## 着色器逻辑

使用 `texelFetch` 手动采样 2x2 像素块（避免 GPU 硬件插值的精度问题），每个采样值乘以 0.25 后累加，生成降采样的 mipmap 级别。EVSM 的矩（moments）对精度非常敏感，因此需要自定义 box 滤波。WebGPU 环境使用独立的 `texelFetch` 调用替代 `texelFetchOffset`。

## 依赖关系

- 无额外依赖
