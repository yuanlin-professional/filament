# TQuatHelpers.h - 四元数运算辅助模板

## 文件概述

通过 CRTP 为四元数类型提供完整的乘法运算符和数学函数。包含四元数代数运算和球面插值等高级功能。

## 核心类

### `TQuatProductOperators<QUATERNION, T>`
四元数乘法运算符：
- `q *= r` -- 四元数复合旋转赋值
- `q * r` -- 四元数 Hamilton 积：`(q.w*r.w - dot(q.xyz, r.xyz), q.w*r.xyz + r.w*q.xyz + cross(q.xyz, r.xyz))`
- `q * vec3` -- 四元数旋转向量：`imaginary(q * quaternion(v, 0) * inverse(q))`
- `q * scalar`, `scalar * q`, `q / scalar` -- 标量缩放

### `TQuatFunctions<QUATERNION, T>`
四元数数学函数：

**基本运算**：
- `dot(p, q)` -- 四元数点积
- `norm(q)` / `length(q)` -- 模长
- `length2(q)` -- 模长平方
- `normalize(q)` -- 归一化
- `conj(q)` -- 共轭 `(w, -x, -y, -z)`
- `inverse(q)` -- 逆 `conj(q) / dot(q, q)`

**分解**：
- `real(q)` -- 实部 w
- `imaginary(q)` -- 虚部 xyz
- `unreal(q)` -- 纯虚四元数
- `cross(p, q)` -- 四元数叉积

**指数映射**：
- `exp(q)` -- 四元数指数
- `log(q)` -- 四元数对数
- `pow(q, a)` -- 四元数幂

**插值**：
- `slerp(p, q, t)` -- 球面线性插值，处理了近似相等和数值稳定性
- `lerp(p, q, t)` -- 线性插值
- `nlerp(p, q, t)` -- 归一化线性插值

**工具**：
- `positive(q)` -- 确保 w >= 0（翻转至正半球）

## 关键实现逻辑

**slerp** 实现细节：先计算点积判断两四元数的夹角；对几乎相等的四元数退化为 nlerp 避免除零；正常情况使用 `sin((1-t)*a) / sin(a)` 和 `sin(t*a) / sin(a)` 混合；通过点积符号确保取最短路径。

## 依赖关系

- `<math/compiler.h>`, `<math/scalar.h>`, `<math/vec3.h>`
