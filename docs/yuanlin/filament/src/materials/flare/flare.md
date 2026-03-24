# Flare (镜头光晕)

## 文件概述

`flare/` 目录包含镜头光晕效果的实现（`flare.h`/`flare.cpp`/`flare.mat`），模拟强光源在镜头内部产生的散射和反射。

## 核心类/结构体

- `flare.h`/`flare.cpp` 提供光晕效果的 C++ 端参数配置和通道调度。
- `flare.mat` 定义光晕后处理材质。

## 关键实现逻辑

- 从 Bloom 降采样结果中提取高亮区域作为光晕源。
- 通过光学模拟（ghost、starburst 等元素）生成多种光晕形态。
- 与 Bloom 效果协同工作，共享降采样链数据。

## 依赖关系

- Bloom 降采样数据
- Filament 后处理管线
