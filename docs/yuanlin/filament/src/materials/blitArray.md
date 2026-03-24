# blitArray

## 文件概述

`blitArray.mat` 定义了一个从 2D 纹理数组中按层和 LOD 级别采样并输出的后处理材质。

## 材质属性

- **域**: `postprocess`
- **深度写入/裁剪**: 均禁用

## 参数

| 名称 | 类型 | 说明 |
|------|------|------|
| `color` | sampler2dArray | 源纹理数组 |
| `layerIndex` | int | 要采样的层索引 |
| `levelOfDetail` | float | LOD 级别 |
| `viewport` | float4 | 视口（xy 偏移 + zw 尺寸） |

## 着色器逻辑

顶点着色器根据 `viewport` 参数重映射 UV 坐标，片段着色器从纹理数组的指定层和 LOD 级别采样。

## 依赖关系

- 无额外依赖
