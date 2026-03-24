# mat3.h - 3x3 矩阵

## 文件概述

定义模板类 `TMat33<T>`，表示 3x3 列主序矩阵。是渲染中最常用的矩阵类型之一，用于法线变换、旋转表示、切线空间等。

## 核心类

### `details::TMat33<T>`
继承自：`TVecUnaryOperators`, `TVecComparisonOperators`, `TVecAddOperators`, `TMatProductOperators`, `TMatSquareFunctions`, `TMatTransform`, `TMatHelpers`

**构造函数**：
- `TMat33()` -- 单位矩阵
- `TMat33(U v)` -- 对角标量
- `TMat33(TVec3 v)` -- 对角向量
- `TMat33(TVec3 v0, TVec3 v1, TVec3 v2)` -- 三个列向量
- `TMat33(9 个标量)` -- 列主序元素
- `TMat33(TQuaternion<U> q)` -- 从四元数构造旋转矩阵
- `TMat33(row_major_init(...))` -- 行主序辅助

**核心静态方法**：
- `getTransformForNormals(TMat33 m)` -- 返回法线变换矩阵（余因子矩阵），比逆转置更高效
- `lookTo(direction, up)` -- 构造朝向 -Z 的相机姿态矩阵
- `packTangentFrame(TMat33 m, storageSize)` -- 将切线空间打包为四元数，保留反射信息
- `orthogonalize(TMat33 m)` -- Gram-Schmidt 正交化

### 自由函数
- `prescaleForNormals(TMat33 m)` -- 按最大缩放因子的倒数预缩放矩阵，避免着色器中的数值溢出

## 关键实现逻辑

**四元数构造**：使用标准的四元数到旋转矩阵公式 `R = I + 2s * skew(v) + 2 * skew(v)^2`。

**packTangentFrame**：将 3x3 正交基编码为四元数，w 分量的符号编码反射信息，使用 bias 确保 w 不为零（适配 SNORM16 存储）。

## 类型别名

- `mat3` = `TMat33<double>`, `mat3f` = `TMat33<float>`

## 依赖关系

- `<math/compiler.h>`, `<math/quat.h>`, `<math/vec3.h>`, `<math/TMatHelpers.h>`, `<math/TVecHelpers.h>`
