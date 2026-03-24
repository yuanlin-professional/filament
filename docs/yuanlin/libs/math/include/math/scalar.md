# scalar.h - 标量数学常量与函数

## 文件概述

定义数学常量（PI、E 等）和基本标量函数（clamp、mix、smoothstep 等）。是整个数学库的基础，几乎所有其他文件都依赖它。

## 核心定义

### 数学常量（double 精度前缀 F_）
- `F_PI` = 3.14159...、`F_PI_2` = PI/2、`F_PI_4` = PI/4
- `F_E` = 2.71828...（自然对数底数）
- `F_TAU` = 2*PI
- `F_LOG2E`, `F_LOG10E`, `F_LN2`, `F_LN10`
- `F_1_PI` = 1/PI, `F_2_PI` = 2/PI
- `F_SQRT2`, `F_SQRT1_2`

### 命名空间 `d`（double）和 `f`（float）
将上述常量分别以 double 和 float 版本提供，并增加：
- `DEG_TO_RAD` = PI / 180
- `RAD_TO_DEG` = 180 / PI

### 标量函数
- `min(a, b)` / `max(a, b)` -- 最小/最大值
- `clamp(v, min, max)` -- 值限制
- `saturate(v)` -- 等价于 `clamp(v, 0, 1)`
- `mix(x, y, a)` / `lerp(x, y, a)` -- 线性插值
- `smoothstep(e0, e1, x)` -- Hermite 平滑过渡：`t^2 * (3 - 2t)`
- `sign(x)` -- 符号函数，返回 -1 或 +1

## 依赖关系

- `<math/compiler.h>`
