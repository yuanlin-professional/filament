# SSAO (Screen-Space Ambient Occlusion)

## 文件概述

`ssao/` 目录包含屏幕空间环境光遮蔽的完整实现（`ssao.h`/`ssao.cpp` 及多个 `.mat`/`.fs` 文件），支持 SAO（Scalable Ambient Obscurance）和 GTAO（Ground Truth Ambient Occlusion）两种算法。

## 材质文件

| 文件 | 说明 |
|------|------|
| `sao.mat` | SAO 算法主通道 |
| `saoBentNormals.mat` | SAO 弯曲法线变体 |
| `gtao.mat` | GTAO 算法主通道 |
| `gtaoBentNormals.mat` | GTAO 弯曲法线变体 |
| `bilateralBlur.mat` | 双边模糊 |
| `bilateralBlurBentNormals.mat` | 带弯曲法线的双边模糊 |
| `mipmapDepth.mat` | 深度 mipmap 生成 |

## 核心着色器

- `saoImpl.fs` / `gtaoImpl.fs`: 各算法的核心实现
- `ssaoUtils.fs`: 共享工具函数
- `ssct.fs` / `ssctImpl.fs`: 屏幕空间锥体追踪（Screen-Space Cone Tracing）

## 关键实现逻辑

- **SAO**: 基于 McGuire 等人的 Scalable Ambient Obscurance，在多个尺度上采样深度缓冲计算遮蔽。
- **GTAO**: 基于 Jimenez 等人的 Ground Truth AO，在半球切片上积分可见性，物理精度更高。
- **双边模糊**: 保边模糊，使用深度差异作为权重防止遮蔽信息跨越深度边界扩散。
- **弯曲法线**: 可选的弯曲法线输出，用于改善间接光照的方向性。
- **深度 mipmap**: 预生成的深度 mipmap 链加速大范围采样。

## 依赖关系

- `ssao.h`/`ssao.cpp` - C++ 端管线调度
- 深度缓冲和（可选的）法线缓冲
- `AmbientOcclusionOptions` - 配置选项
