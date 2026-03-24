# SGSR (Snapdragon Game Super Resolution)

## 文件概述

`sgsr/` 目录包含 Qualcomm Snapdragon Game Super Resolution 的实现（`sgsr.h`/`sgsr.cpp`/`sgsr1.mat`/`sgsr1_shader_mobile.fs`），针对 Adreno GPU 优化的空间上采样技术。

## 核心文件

- `sgsr.h`/`sgsr.cpp`: C++ 端配置和调度
- `sgsr1.mat`: SGSR 后处理材质
- `sgsr1_shader_mobile.fs`: 移动端优化的片段着色器

## 关键实现逻辑

- 针对 Qualcomm Adreno GPU 架构优化的超分辨率算法。
- 与 FSR 类似，通过空间滤波实现低分辨率到高分辨率的上采样。
- 专门为移动端 GPU 的指令集和内存访问模式优化。

## 依赖关系

- `LICENSE.txt` - Qualcomm 许可证
- Filament 后处理管线
