# resolveDepth

## 文件概述

`resolveDepth.mat` 定义了多采样深度解析材质，将多采样深度纹理解析为单采样深度。

## 材质属性

- **域**: `postprocess`
- **深度写入**: 启用
- **深度裁剪**: 禁用
- **裁剪**: 无

## 参数

| 名称 | 类型 | 说明 |
|------|------|------|
| `depth` | sampler2d | 多采样深度纹理（multisample, 不可过滤） |

## 着色器逻辑

使用 `texelFetch` 从多采样纹理的第 0 个样本获取深度值，类似于 `glBlitFramebuffer()` 的行为。目前仅用于 Metal 和 Vulkan 后端。

## 依赖关系

- 无额外依赖
