# vec3.h - 三维向量

## 文件概述

定义模板类 `TVec3<T>`，表示三维向量。是渲染引擎中使用最频繁的数学类型之一，用于表示位置、方向、颜色（RGB）等。

## 核心类

### `details::TVec3<T>`
继承自：`TVecProductOperators`, `TVecAddOperators`, `TVecUnaryOperators`, `TVecComparisonOperators`, `TVecFunctions`

**数据布局**（嵌套 union）：
- `T v[3]` -- 数组访问
- `TVec2<T> xy` / `{x, y, z}` -- 坐标 swizzle
- `TVec2<T> rg` / `{r, g, b}` -- 颜色通道 swizzle
- `TVec2<T> st` / `{s, t, p}` -- 纹理坐标 swizzle
- `TVec2<T> yz`, `TVec2<T> gb` 等子向量访问

**构造函数**：
- `TVec3(A v)` -- 广播标量
- `TVec3(A x, B y, C z)` -- 分量构造
- `TVec3(TVec2<A> v, B z)` -- 从 vec2 + 标量构造
- `TVec3(TVec3<A>)` -- 类型转换

**特有函数**：
- `cross(TVec3, TVec3)` -- 三维叉积，返回 `TVec3`

## 类型别名

`float3`, `double3`, `half3`, `int3`, `uint3`, `short3`, `ushort3`, `byte3`, `ubyte3`, `bool3`

## 依赖关系

- `<math/half.h>`, `<math/vec2.h>` -- 基础类型
