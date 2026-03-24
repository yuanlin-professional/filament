# ImageOps.h / ImageOps.cpp

## 文件概述

提供对 `LinearImage` 的各种图像操作函数，包括拼接、翻转、转置、裁剪、通道提取/合并、距离场计算、Voronoi 图生成等。类似于 NumPy 的图像操作接口。

## 核心类/结构体/函数

- **`horizontalStack()`** / **`verticalStack()`** - 水平/垂直拼接多张图像
- **`horizontalFlip()`** / **`verticalFlip()`** - 水平/垂直翻转图像
- **`vectorsToColors()`** / **`colorsToVectors()`** - 法线向量 [-1,1] 与颜色 [0,1] 互转
- **`extractChannel()`** - 提取单个通道为单通道图像
- **`combineChannels()`** - 将多个单通道图像合并为多通道图像
- **`transpose()`** - 转置图像（行列互换，实际移动数据）
- **`cropRegion()`** - 按指定矩形裁剪图像
- **`compare()`** - 字典序比较两张图像
- **`clearToValue()`** - 将图像所有像素设为指定值
- **`computeCoordField()`** - 计算坐标场（距离场/Voronoi 图的前置步骤）
- **`edtFromCoordField()`** - 从坐标场生成欧几里得距离场
- **`voronoiFromCoordField()`** - 从坐标场生成 Voronoi 图或膨胀图像
- **`blitImage()`** - 拷贝源图像数据到目标图像

## 关键实现逻辑

- 垂直拼接通过先转置、水平拼接、再转置来实现（因为行优先存储，直接操作列会很痛苦）
- 距离场算法基于 Felzenszwalb 和 Huttenlocher 的论文 "Distance Transforms of Sampled Functions"，使用抛物线下包络线方法
- 坐标场分两步计算：先水平方向 EDT，转置后再做一次水平 EDT，最终得到 2D 坐标场

## 依赖关系

- `image/LinearImage.h` - 线性图像数据结构
- `math/vec3.h`, `math/vec4.h` - 数学向量
- `utils/Panic.h` - 断言检查
