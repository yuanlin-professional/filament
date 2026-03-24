# ImageSampler.h / ImageSampler.cpp

## 文件概述

图像重采样库，提供高质量的图像缩放、模糊、mipmap 生成和单点采样功能。支持多种滤波器（Box、Nearest、Hermite、Gaussian、Mitchell、Lanczos 等），采用可分离滤波器的两阶段实现。

## 核心类/结构体/函数

- **`Filter`** 枚举 - 滤波器类型：BOX、NEAREST、HERMITE、GAUSSIAN_SCALARS/NORMALS、MITCHELL、LANCZOS、MINIMUM
- **`SingleSample`** - 单点采样结果容器，按通道数分配内存
- **`Region`** - 源图像中的视口定义（归一化坐标）
- **`Boundary`** - 采样边界行为：EXCLUDE、CLAMP、REPEAT、MIRROR、COLOR、NEIGHBOR 等
- **`ImageSampler`** - 重采样配置结构体，包含水平/垂直滤波器、源区域和边界设置
- **`resampleImage()`** - 两个重载：完整配置版和简化版（仅指定尺寸和滤波器）
- **`computeSingleSample()`** - 计算单个纹理坐标处的采样值
- **`generateMipmaps()`** - 从原始图像生成指定数量的 mipmap 级别
- **`getMipmapCount()`** - 计算达到 1x1 所需的 mipmap 层数
- **`filterFromString()`** - 字符串到 Filter 枚举的转换

## 关键实现逻辑

- 使用 MAD（Multiply-Add）指令列表缓存滤波权重，避免重复计算
- 可分离滤波：先水平方向重采样，转置，再垂直方向重采样，最后转置回来
- 滤波器函数包括：Mitchell-Netravali 三次滤波器（放大默认）和 Lanczos sinc 滤波器（缩小默认）
- MINIMUM 滤波器取最小值而非加权平均，适用于深度图和 SDF
- mipmap 生成直接从原始图像生成所有级别（而非逐级降采样），以获得更高质量

## 依赖关系

- `image/LinearImage.h`, `image/ImageOps.h` - 图像基础设施
- `math/scalar.h`, `math/vec3.h`, `math/vec4.h` - 数学库
- `utils/Panic.h` - 断言
