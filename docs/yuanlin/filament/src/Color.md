# Color.cpp

## 文件概述

`Color` 类的实现文件，提供颜色空间转换和色温计算功能。包括 sRGB 与线性空间之间的转换、色温到 RGB 的转换（CCT 和 D 光源系列），以及用于体积吸收效果的吸收系数计算。

## 核心类/结构体/函数

### `Color` 类静态方法

- **`sRGBToLinear(float3)`** -- 将 sRGB 颜色转换为线性空间，调用 `EOTF_sRGB`。
- **`linearToSRGB(float3)`** -- 将线性颜色转换为 sRGB 空间，调用 `OETF_sRGB`。
- **`cct(float K)`** -- 根据色温（开尔文）计算 sRGB 线性颜色。先转换到 CIE 1960 色空间，再转换到 sRGB。
- **`illuminantD(float K)`** -- 根据 CIE D 光源系列的色温计算 sRGB 线性颜色。
- **`absorptionAtDistance(LinearColor, float distance)`** -- 计算给定距离下的体积吸收系数，使用 Beer-Lambert 定律。

## 关键实现逻辑

- **色温转换**：`cct` 使用 Krystek 近似公式将色温转换为 CIE 1960 UCS 坐标 (u,v)，然后通过 `XYZ_to_sRGB` 矩阵转换到 sRGB 空间。
- **D 光源计算**：`illuminantD` 使用 CIE 标准公式根据色温计算 D 光源的色度坐标 (x,y)，再转换到线性 sRGB。
- **结果归一化**：两种色温方法都将结果归一化到 [0,1] 范围并进行饱和处理。

## 依赖关系

- `filament/Color.h` -- 公共头文件
- `ColorSpaceUtils.h` -- 颜色空间转换矩阵和 EOTF/OETF 函数
- `math/mat3.h`、`math/scalar.h` -- 数学运算

## 被引用关系

被光照系统、材质系统和后处理管线使用，用于颜色空间转换和光源色温计算。
