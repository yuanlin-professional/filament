# DOF (Depth of Field)

## 文件概述

`dof/` 目录包含景深（Depth of Field）后处理效果的完整实现（`dof.h`/`dof.cpp` 及多个 `.mat` 和 `.fs` 文件），模拟相机光圈的散焦效果。

## 材质文件

| 文件 | 说明 |
|------|------|
| `dof.mat` | 主景深渲染通道 |
| `dofCoc.mat` | CoC（Circle of Confusion）计算 |
| `dofCombine.mat` | 前景/背景/焦点合成 |
| `dofDilate.mat` | CoC 膨胀 |
| `dofDownsample.mat` | 降采样 |
| `dofMedian.mat` | 中值滤波 |
| `dofMipmap.mat` | Mipmap 生成 |
| `dofTiles.mat` | Tile 分类 |
| `dofTilesSwizzle.mat` | Tile Swizzle 通道 |

## 核心类/结构体

- `dof.h`/`dof.cpp` 提供多通道景深管线的 C++ 调度逻辑。
- `dofUtils.fs` 包含共享的着色器工具函数。

## 关键实现逻辑

- **CoC 计算**: 基于深度值和相机光圈参数计算每个像素的散焦圆大小。
- **Tile 分类**: 将画面分为 tile，根据最大 CoC 值分类为前景/背景/焦点区域。
- **散焦模糊**: 使用散点图（scatter-as-gather）技术，根据 CoC 大小从 mipmap 链中采样。
- **前景/背景分离**: 前景和背景散焦分别处理，避免边缘渗色。
- **合成**: 将散焦结果与原始锐利图像按 CoC 混合。

## 依赖关系

- `dofUtils.fs` - 共享着色器工具
- 深度缓冲 - 用于 CoC 计算
