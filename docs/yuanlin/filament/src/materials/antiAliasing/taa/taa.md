# TAA (Temporal Anti-Aliasing)

## 文件概述

`antiAliasing/taa/` 目录包含 TAA 时间性抗锯齿的实现（`taa.h`/`taa.cpp`/`taa.mat`），通过在时间维度上累积多帧采样来消除锯齿。

## 核心类/结构体

- TAA 模块通过 `.cpp`/`.h` 文件提供 C++ 端的抖动（jitter）配置和历史缓冲管理。
- `taa.mat` 定义了后处理材质。

## 关键实现逻辑

- 每帧对相机投影矩阵施加亚像素抖动（jitter），使采样点在像素内移动。
- 使用运动向量（motion vectors）将当前帧与前一帧的颜色进行重投影和混合。
- 包含历史拒绝机制防止鬼影：当运动向量不可靠或场景变化剧烈时降低历史帧权重。
- 质量接近 MSAA 但适用于延迟渲染，开销约等于一次全屏后处理。

## 依赖关系

- 历史颜色缓冲 / 运动向量缓冲
- `TemporalAntiAliasingOptions` - TAA 配置选项
