# debugShadowCascades

## 文件概述

`debugShadowCascades.mat` 定义了阴影级联调试可视化材质，在屏幕上用不同颜色标记各个阴影级联的覆盖区域。

## 材质属性

- **域**: `postprocess`
- **深度写入/裁剪**: 均禁用

## 参数

| 名称 | 类型 | 说明 |
|------|------|------|
| `color` | sampler2d | 场景颜色纹理 |
| `depth` | sampler2d | 场景深度纹理（不可过滤） |
| `cascadeSplits` | float4 | 级联分割点 |
| `cascadeCount` | int | 级联数量 |
| `shadowAtlasResolution` | float2 | 阴影图集分辨率 |
| `lightFromWorldMatrix` | mat4[4] | 光空间矩阵（每级联一个） |
| `scissorNormalized` | float4[4] | 归一化裁剪矩形 |

## 着色器逻辑

- **级联颜色映射**: 级联 0=绿色，1=蓝色，2=黄色，3=红色，4=紫色，5=青色。
- **级联判定 (`getShadowCascade`)**: 将片段的视空间深度与 `cascadeSplits` 比较，确定所属级联。
- **输出**: 场景颜色乘以级联对应的调试颜色。

## 依赖关系

- `frameUniforms` - 帧级 uniform（`getViewFromClipMatrix`）
