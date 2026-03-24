# Bloom

## 文件概述

`bloom/` 目录包含 Bloom（泛光）效果的实现（`bloom.h`/`bloom.cpp` 及多个 `.mat` 文件），通过多级降采样和升采样实现高亮区域的光晕扩散效果。

## 材质文件

| 文件 | 说明 |
|------|------|
| `bloomDownsample.mat` | 标准降采样通道 |
| `bloomDownsample2x.mat` | 2 倍降采样通道 |
| `bloomDownsample9.mat` | 9-tap 降采样通道 |
| `bloomUpsample.mat` | 升采样通道 |

## 核心类/结构体

- `bloom.h`/`bloom.cpp` 提供 C++ 端的 Bloom 效果管理，包括阈值设置、强度控制和多级通道调度。

## 关键实现逻辑

- **降采样链**: 从全分辨率颜色缓冲开始，逐级降采样（通常 5-7 级），每级分辨率减半。使用不同的降采样 kernel（标准/2x/9-tap）在质量和性能间权衡。
- **升采样链**: 从最低分辨率开始逐级升采样并与上一级混合，形成多级光晕效果。
- **阈值处理**: 只有超过亮度阈值的像素才参与 Bloom 计算。

## 依赖关系

- Filament 后处理管线
- `bloom.h`/`bloom.cpp` - C++ 调度逻辑
