# vec2.h - 二维向量

## 文件概述

定义模板类 `TVec2<T>`，表示二维向量。通过 CRTP 多重继承获得完整的算术运算、比较运算和数学函数支持。同时定义了常用的类型别名如 `float2`、`double2`、`int2` 等。

## 核心类

### `details::TVec2<T>`
继承自：`TVecProductOperators`, `TVecAddOperators`, `TVecUnaryOperators`, `TVecComparisonOperators`, `TVecFunctions`

**数据布局**（union）：
- `T v[2]` -- 数组访问
- `{x, y}` -- 坐标访问
- `{s, t}` -- 纹理坐标访问
- `{r, g}` -- 颜色通道访问

**构造函数**：
- `TVec2()` -- 默认构造
- `TVec2(A v)` -- 广播标量到两个分量
- `TVec2(A x, B y)` -- 分量构造
- `TVec2(const TVec2<A>&)` -- 类型转换构造

**特有函数**：
- `cross(TVec2, TVec2)` -- 二维叉积，返回标量（z 分量值）

## 类型别名

`float2`, `double2`, `half2`, `int2`, `uint2`, `short2`, `ushort2`, `byte2`, `ubyte2`, `bool2`

## 依赖关系

- `<math/TVecHelpers.h>` -- 运算符和函数基类
- `<math/half.h>` -- half 类型支持
