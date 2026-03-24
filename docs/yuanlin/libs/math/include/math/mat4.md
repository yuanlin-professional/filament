# mat4.h - 4x4 矩阵

## 文件概述

定义模板类 `TMat44<T>`，表示 4x4 列主序矩阵。是 3D 渲染管线的核心类型，用于模型变换、视图变换、投影变换等。

## 核心类

### `details::TMat44<T>`
继承自：`TVecUnaryOperators`, `TVecComparisonOperators`, `TVecAddOperators`, `TMatProductOperators`, `TMatSquareFunctions`, `TMatTransform`, `TMatHelpers`

**构造函数**：
- `TMat44()` -- 单位矩阵
- `TMat44(U v)` -- 对角标量
- `TMat44(TVec4 v)` -- 对角向量
- `TMat44(4 个 TVec4)` -- 四个列向量
- `TMat44(16 个标量)` -- 列主序元素
- `TMat44(TQuaternion q)` -- 从四元数构造
- `TMat44(TMat33 m)` -- 从 3x3 矩阵构造（右下角为 1）
- `TMat44(TMat33 m, TVec3 t)` -- 3x3 旋转 + 平移
- `TMat44(TMat33 m, TVec4 col3)` -- 3x3 + 第四列

**投影矩阵工厂**：
- `ortho(left, right, bottom, top, near, far)` -- 正交投影
- `frustum(left, right, bottom, top, near, far)` -- 透视截锥投影
- `perspective(fov, aspect, near, far, direction)` -- 透视投影（支持水平/垂直 FOV）

**视图矩阵工厂**：
- `lookAt(eye, center, up)` -- 经典 lookAt 视图矩阵
- `lookTo(direction, position, up)` -- 方向+位置构造视图矩阵

**工具方法**：
- `upperLeft()` -- 提取左上 3x3 子矩阵
- `project(projMatrix, vertex)` -- 投影顶点（透视除法）
- `translation(TVec3)`, `scaling(TVec3/scalar)` -- 变换矩阵工厂

### 高精度乘法函数
- `highPrecisionMultiply(mat4f, float4/mat4f)` -- 使用 `double` 中间精度的矩阵乘法
- `highPrecisionMultiplyd(mat4f, float4/mat4f)` -- 返回 `double` 精度结果

## 关键实现逻辑

**perspective**：通过 FOV 角度计算半宽/半高后调用 `frustum`。支持垂直和水平 FOV 方向。

**lookAt**：先归一化方向向量，再委托给 `TMat33::lookTo` 构建旋转部分，最后组合平移。

## 类型别名

- `mat4` = `TMat44<double>`, `mat4f` = `TMat44<float>`

## 依赖关系

- `<math/TMatHelpers.h>`, `<math/compiler.h>`, `<math/mat3.h>`, `<math/quat.h>`, `<math/scalar.h>`, `<math/vec3.h>`, `<math/vec4.h>`
