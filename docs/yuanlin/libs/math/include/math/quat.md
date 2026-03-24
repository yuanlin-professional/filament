# quat.h - 四元数

## 文件概述

定义模板类 `TQuaternion<T>`，表示四元数 `q = w + xi + yj + zk`。用于 3D 旋转表示，相比旋转矩阵占用更少内存且支持球面线性插值（slerp）。

## 核心类

### `details::TQuaternion<T>`
继承自：`TVecAddOperators`, `TVecUnaryOperators`, `TVecComparisonOperators`, `TQuatProductOperators`, `TQuatFunctions`

**数据布局**（union）：
- `{x, y, z, w}` -- 分量访问
- `TVec4<T> xyzw` -- 四维向量访问
- `TVec3<T> xyz` -- 虚部向量
- `TVec2<T> xy` -- 前两个分量

**存储顺序**：`q[0]=x, q[1]=y, q[2]=z, q[3]=w`

**构造函数**：
- `TQuaternion()` -- 零四元数
- `TQuaternion(A w)` -- 实数四元数（隐式转换）
- `TQuaternion(A w, B x, C y, D z)` -- 四分量
- `TQuaternion(TVec3 v, B w)` -- 虚部+实部
- `TQuaternion(TVec4 v)` -- 从四维向量（显式）

**静态工厂**：
- `fromAxisAngle(axis, angle)` -- 从旋转轴和角度创建
- `fromDirectedRotation(from, to)` -- 从方向向量构造最短弧旋转，处理了平行和反平行退化情况

**运算符**：
- `operator~()` -- 共轭（conj）

## 类型别名

- `quat` = `TQuaternion<double>`, `quatf` = `TQuaternion<float>`, `quath` = `TQuaternion<half>`
- 用户字面量：`1.0_i`, `1.0_j`, `1.0_k`

## 关键实现逻辑

`fromDirectedRotation` 使用 Stan Melax 的算法：计算 cosTheta = dot(from, to)，对退化情况（同向/反向）特殊处理，正常情况通过 `cross + sqrt(2*(1+cosTheta))` 构造四元数。

## 依赖关系

- `<math/TQuatHelpers.h>`, `<math/compiler.h>`, `<math/half.h>`, `<math/vec3.h>`, `<math/vec4.h>`
