# vec4.h - 四维向量

## 文件概述

定义模板类 `TVec4<T>`，表示四维向量。用于齐次坐标、RGBA 颜色、平面方程等场景，也是矩阵列向量的类型。

## 核心类

### `details::TVec4<T>`
继承自：`TVecProductOperators`, `TVecAddOperators`, `TVecUnaryOperators`, `TVecComparisonOperators`, `TVecFunctions`

**数据布局**（深层嵌套 union）：
- `T v[4]` -- 数组访问
- `TVec2<T> xy`, `TVec3<T> xyz` -- 子向量 swizzle
- `{x, y, z, w}` -- 齐次坐标
- `{r, g, b, a}` -- 颜色通道
- `{s, t, p, q}` -- 纹理坐标
- `TVec2<T> zw`, `TVec3<T> yzw` 等多种子向量组合

**构造函数**：
- `TVec4(A v)` -- 广播标量到四个分量
- `TVec4(A x, B y, C z, D w)` -- 分量构造
- `TVec4(TVec2<A>, B z, C w)` -- vec2 + 两个标量
- `TVec4(TVec2<A>, TVec2<B>)` -- 两个 vec2 拼接
- `TVec4(TVec3<A>, B w)` -- vec3 + w 分量
- `TVec4(TVec4<A>)` -- 类型转换

## 类型别名

`float4`, `double4`, `half4`, `int4`, `uint4`, `short4`, `ushort4`, `byte4`, `ubyte4`, `bool4`

## 依赖关系

- `<math/half.h>`, `<math/vec3.h>` -- 基础类型和子向量类型
