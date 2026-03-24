# ColorSpaceUtils.h / ColorSpaceUtils.cpp

## 文件概述

提供全面的颜色空间转换工具，包含大量预定义的色空间转换矩阵、传递函数（EOTF/OETF）、色空间转换函数以及色域映射（gamut mapping）算法。覆盖 sRGB、Rec.2020、ACEScg（AP1）、ACES（AP0）、OkLab、ICtCp 等多种颜色空间。

## 核心类/结构体/函数

### 类型定义

- **`xyY`** / **`XYZ`** -- CIE 色度坐标和三刺激值类型（均为 `float3` 别名）。

### 颜色空间转换矩阵（`constexpr mat3f`）

包含约 30 个预定义矩阵，覆盖以下色空间之间的转换路径：
- sRGB / Rec.709、Rec.2020、XYZ、CIECAT16 LMS、AP0（ACES）、AP1（ACEScg）、OkLab LMS

### 传递函数

- **`OETF_sRGB` / `EOTF_sRGB`** -- sRGB 光电/电光传递函数。
- **`OETF_PQ` / `EOTF_PQ`** -- PQ (Perceptual Quantizer, ST 2084) 传递函数，用于 HDR。
- **`LogC_to_linear` / `linear_to_LogC`** -- ARRI Alexa LogC EI 1000 曲线。
- **`ACEScct_to_linearAP1` / `linearAP1_to_ACEScct`** -- ACEScct 编码转换。

### 颜色空间转换函数

- **`xyY_to_XYZ` / `XYZ_to_xyY`** -- CIE 色度坐标转换。
- **`sRGB_to_OkLab` / `OkLab_to_sRGB`** -- sRGB 与 OkLab 感知色空间之间的转换。
- **`Rec2020_to_ICtCp` / `ICtCp_to_Rec2020`** -- Rec.2020 与 ICtCp 之间的转换。

### 色域映射

- **`gamutMapping_sRGB(float3)`** -- 将超出 sRGB 色域的颜色映射回色域内。

## 关键实现逻辑

- **色域映射算法**：`ColorSpaceUtils.cpp` 实现了 Bjorn Ottosson 的自适应色域裁剪算法（基于 OkLab 色空间）。通过在 OkLab 空间中计算色域边界（`find_cusp`），使用 Halley 方法迭代求解最大饱和度，然后沿亮度-色度路径裁剪。
- **阈值容差**：色域映射包含可配置的 `threshold` 参数（默认 0.03），对轻微超出色域的值进行容差处理，避免不必要的裁剪。
- **编译期矩阵组合**：复合转换矩阵（如 `sRGB_to_Rec2020`）在编译期通过 `constexpr` 矩阵乘法预计算。

## 依赖关系

- `math/mat3.h`、`math/vec3.h`、`math/scalar.h` -- 数学库
- `utils/compiler.h` -- 编译器宏

## 被引用关系

被 `Color.cpp`、色调映射（Tone Mapping）、颜色分级（Color Grading）等后处理模块广泛使用。是 Filament 颜色管线的基础设施。
