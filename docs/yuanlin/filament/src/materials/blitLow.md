# blitLow

## 文件概述

`blitLow.mat` 定义了低特性级别（Feature Level 0）兼容的纹理 blit 材质。

## 材质属性

- **域**: `postprocess`
- **特性级别**: 0
- **深度写入/裁剪**: 均禁用

## 参数

| 名称 | 类型 | 说明 |
|------|------|------|
| `color` | sampler2d | 源纹理 |
| `viewport` | float4 | 视口参数 |
| `levelOfDetail` | float | LOD 级别 |

## 着色器逻辑

在 GLES2 兼容的 Feature Level 0 下使用 `texture2D`，更高版本使用 `textureLod`。视口重映射逻辑与 blitArray 相同。

## 依赖关系

- 无额外依赖
