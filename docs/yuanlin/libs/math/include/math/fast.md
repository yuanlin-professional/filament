# fast.h - 快速数学近似函数

## 文件概述

提供高性能的数学近似函数，用于替代标准库中较慢的数学实现。所有函数位于 `filament::math::fast` 命名空间下。适用于对精度要求不高但对性能敏感的渲染场景，例如着色器预计算。

## 核心函数

### 三角函数
- `cos(T x)` -- 快速余弦近似（约 8 周期，ARM 上标准版约 66 周期），x 范围 [-pi, pi]，使用多项式逼近
- `sin(T x)` -- 基于 `cos(x - PI/2)` 实现的快速正弦

### 对数函数
- `ilog2(float x)` -- 通过 IEEE 754 位操作提取整数部分的 log2
- `log2(float x)` -- 快速 log2 近似，使用位操作 + 二次多项式拟合

### 平方根倒数
- `isqrt(float/double x)` -- 快速 `1/sqrt(x)`，AArch64 上使用 NEON 指令 `vrsqrtes_f32` + 一次牛顿迭代

### 编译期数学
- `pow(double x, unsigned y)` -- constexpr 递归幂运算
- `factorial(unsigned x)` -- constexpr 递归阶乘
- `exp(double/float x)` -- constexpr 泰勒级数展开（9 阶），用于编译期常量计算

### 饱和算术
- `qadd(T a, T b)` -- 无符号饱和加法，AArch64 使用 NEON intrinsic
- `qsub(T a, T b)` -- 无符号饱和减法
- `qinc(T a)` / `qdec(T a)` -- 饱和自增/自减

### 符号位
- `signbit(float x)` -- 优先使用 `__builtin_signbitf`，避免 Android NDK 上的函数调用开销

## 关键实现逻辑

`cos` 使用四阶多项式逼近，先将输入归一化到 [-0.5, 0.5] 范围，再用 `16|x| - 8` 和一次修正项 `0.225 * x * (|x| - 1)` 提高精度。该实现可被编译器自动向量化。

## 依赖关系

- `<math/compiler.h>`, `<math/scalar.h>`
- `<cmath>`, `<type_traits>`, `<stdint.h>`
- `<arm_neon.h>`（条件编译，仅 ARM NEON 平台）
