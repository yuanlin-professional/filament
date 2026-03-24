# TMatHelpers.h - 矩阵运算辅助模板

## 文件概述

通过 CRTP 为所有矩阵类型提供通用的矩阵运算，包括求逆、转置、行列式、余因子矩阵、矩阵乘法等。同时提供欧拉角构造和四元数提取功能。是矩阵类的核心运算基础设施。

## 核心类/函数

### `matrix` 命名空间下的算法

**矩阵求逆**：
- `gaussJordanInverse(MATRIX)` -- 通用高斯-约旦消元法求逆（用于 4x4 及更大矩阵）
- `fastInverse2(MATRIX)` -- 2x2 解析求逆：`adj(M) / det(M)`
- `fastInverse3(MATRIX)` -- 3x3 解析求逆：使用 Sarrus 法则计算行列式

**行列式与余因子**：
- `Determinant<T, O>` -- 递归模板计算行列式（特化了 1x1、2x2、3x3）
- `cofactor(MATRIX)` -- 通用余因子矩阵（基于子矩阵行列式）
- `fastCofactor2/3` -- 2x2 和 3x3 的解析余因子
- `cof(MATRIX)` -- 自动选择最优余因子算法
- `det(MATRIX)` -- 行列式
- `inverse(MATRIX)` -- 自动选择最优求逆算法

**其他操作**：
- `multiply(A, B)` -- 矩阵乘法
- `transpose(MATRIX)` -- 转置
- `trace(MATRIX)` -- 迹
- `diag(MATRIX)` -- 对角线向量
- `extractQuat(MATRIX)` -- 从旋转矩阵提取四元数（Imath/Eigen 算法）

### CRTP 辅助类

- `TMatProductOperators` -- 矩阵乘法运算符：矩阵*矩阵、矩阵*向量、向量*矩阵、矩阵*标量
- `TMatSquareFunctions` -- 方阵函数（inverse, cof, transpose, trace, det, diag）
- `TMatHelpers` -- 通用辅助（asArray, operator(), abs, 尺寸查询）
- `TMatTransform` -- 3x3/4x4 变换函数（rotation, eulerYXZ, eulerZYX, toQuaternion）

## 关键实现逻辑

**extractQuat**：通过矩阵的迹判断正负，正迹直接计算；负迹时找最大对角元素确定主分量，避免数值不稳定。

**rotation**：对坐标轴旋转使用简化公式，任意轴旋转使用 Rodrigues 公式并 clamp 到 [-1, 1]。

**eulerZYX**：按 Z-Y-X 顺序（yaw-pitch-roll）构造旋转矩阵。

## 依赖关系

- `<math/compiler.h>`, `<math/quat.h>`, `<math/TVecHelpers.h>`
- `<algorithm>`, `<cmath>`
