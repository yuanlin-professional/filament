# mat2.h - 2x2 矩阵

## 文件概述

定义模板类 `TMat22<T>`，表示 2x2 列主序矩阵。用于 2D 变换（旋转、缩放）。通过 CRTP 继承获得矩阵运算能力。

## 核心类

### `details::TMat22<T>`
继承自：`TVecUnaryOperators`, `TVecComparisonOperators`, `TVecAddOperators`, `TMatProductOperators`, `TMatSquareFunctions`, `TMatHelpers`

**内部存储**：`col_type m_value[2]`（2 个 `TVec2<T>` 列向量）

**构造函数**：
- `TMat22()` -- 单位矩阵
- `TMat22(U v)` -- 对角标量矩阵
- `TMat22(TVec2<U> v)` -- 对角向量矩阵
- `TMat22(TVec2 v0, TVec2 v1)` -- 从两个列向量构造
- `TMat22(A m00, B m01, C m10, D m11)` -- 列主序 4 元素
- `TMat22(row_major_init(...))` -- 行主序构造辅助

**静态工厂方法**：
- `rotate(T radian)` -- 2D 旋转矩阵
- `scaling(A s)` / `scaling(TVec2<A> s)` -- 缩放矩阵

## 类型别名

- `mat2` = `TMat22<double>`
- `mat2f` = `TMat22<float>`

## 关键实现逻辑

提供自定义的 `std::swap` 特化，通过逐元素交换生成更优的机器代码（避免 Clang 的堆栈拷贝优化 bug）。

## 依赖关系

- `<math/TMatHelpers.h>`, `<math/compiler.h>`, `<math/vec2.h>`
