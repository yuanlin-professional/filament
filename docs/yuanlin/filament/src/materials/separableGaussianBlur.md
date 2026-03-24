# separableGaussianBlur

## 文件概述

`separableGaussianBlur.mat` 和关联的 `.vs`/`.fs` 文件实现了可分离的高斯模糊后处理效果，支持 2D 纹理和 2D 数组纹理。

## 材质属性

- **域**: `postprocess`
- **深度写入/裁剪**: 均禁用

## 参数

| 名称 | 类型 | 说明 |
|------|------|------|
| `source` | sampler2d | 2D 源纹理 |
| `sourceArray` | sampler2dArray | 2D 数组源纹理 |
| `axis` | float2 | 模糊方向轴 |
| `level` | float | mip 级别 |
| `layer` | float | 数组纹理的层 |
| `count` | int | 卷积核采样数 |
| `reinhard` | int | 是否使用 Reinhard 色调映射 |
| `kernel` | float2[32] | 卷积核（offset + weight） |

## 常量

| 名称 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `arraySampler` | bool | false | 是否使用数组纹理 |
| `componentCount` | int | 4 | 处理的颜色分量数 |

## 着色器逻辑

可分离高斯模糊沿指定轴方向进行一维卷积，通过两次通道（水平 + 垂直）实现完整的二维高斯模糊。支持 Reinhard 色调映射以处理 HDR 内容。

## 依赖关系

- `separableGaussianBlur.vs` - 顶点着色器实现
- `separableGaussianBlur.fs` - 片段着色器实现
