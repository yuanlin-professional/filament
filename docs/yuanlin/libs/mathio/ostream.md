# ostream.h + ostream.cpp - 数学类型的流输出

## 文件概述

为 Filament 数学库的所有核心类型（向量、矩阵、四元数）提供 `std::ostream` 输出支持。头文件声明模板函数，源文件实现格式化逻辑并显式实例化所有常用类型。

## 核心函数

### 内部辅助函数（ostream.cpp）
- `printVector(stream, data, count)` -- 格式化输出向量：`< x, y, z >`
- `printMatrix(stream, m, rows, cols)` -- 格式化输出矩阵，使用 `/`, `|`, `\` 符号绘制边框，固定 10 字符宽度
- `printQuat(stream, q)` -- 格式化输出四元数：`< w + xi + yj + zk >`

### 模板 operator<< 重载（ostream.h 声明）
- `operator<<(ostream, TVec2<T>)` -- 二维向量输出
- `operator<<(ostream, TVec3<T>)` -- 三维向量输出
- `operator<<(ostream, TVec4<T>)` -- 四维向量输出
- `operator<<(ostream, TMat22<T>)` -- 2x2 矩阵输出
- `operator<<(ostream, TMat33<T>)` -- 3x3 矩阵输出
- `operator<<(ostream, TMat44<T>)` -- 4x4 矩阵输出
- `operator<<(ostream, TQuaternion<T>)` -- 四元数输出

### 显式实例化的类型
- 向量：`double`, `float`, `half`, `uint32_t`, `int32_t`, `uint16_t`, `int16_t`, `uint8_t`, `int8_t`, `bool`
- 矩阵：`double`, `float`
- 四元数：`double`, `float`, `half`

## 关键实现逻辑

矩阵按行输出，第一行用 `/` 和 `\`，中间行用 `|`，最后行用 `\` 和 `/`，模拟数学符号的矩阵括号。每个元素使用 `std::to_string` 转换后用 `setw(10)` 对齐。

头文件使用 `MATHIO_PUBLIC` 宏控制符号可见性（`__attribute__((visibility("default")))`）。

## 依赖关系

- `<math/mathfwd.h>` -- 类型前向声明（头文件）
- `<math/mat2.h>`, `<math/mat3.h>`, `<math/mat4.h>`, `<math/vec2.h>` ~ `<math/vec4.h>`, `<math/quat.h>`, `<math/half.h>`（源文件）
- `<iomanip>`, `<ostream>`, `<string>`
