# TVecHelpers.h - 向量运算辅助模板

## 文件概述

通过 CRTP（Curiously Recurring Template Pattern）为所有向量类型提供通用的算术运算符、比较运算符和数学函数。向量类通过继承这些模板类自动获得完整的运算能力。这是数学库的运算基础设施。

## 核心类

### 辅助工具
- `arithmetic_result_t<T, U>` -- 推导两种类型运算后的结果类型
- `enable_if_arithmetic_t<A, B, C, D>` -- SFINAE 约束，确保模板参数为算术类型

### `TVecAddOperators<VECTOR, T>`
提供加减运算：`+=`, `-=`, `+`, `-`（向量-向量、向量-标量）

### `TVecProductOperators<VECTOR, T>`
提供乘除运算：`*=`, `/=`, `*`, `/`（向量-向量逐元素、向量-标量）

### `TVecUnaryOperators<VECTOR, T>`
提供一元取负运算：`operator-()`

### `TVecComparisonOperators<VECTOR, T>`
提供比较运算：`==`, `!=`, `equal`, `notEqual`, `lessThan`, `lessThanEqual`, `greaterThan`, `greaterThanEqual`

### `TVecFunctions<VECTOR, T>`
提供数学函数（均为 friend 函数）：
- 几何函数：`dot`, `norm/length`, `norm2/length2`, `distance`, `distance2`, `normalize`
- 标量数学：`abs`, `floor`, `ceil`, `round`, `sqrt`, `cbrt`, `inversesqrt`, `exp`, `log`, `log2`, `log10`, `pow`, `sign`
- 插值与限制：`clamp`, `saturate`, `mix`, `smoothstep`, `step`, `fma`, `min`, `max`
- 逻辑函数：`any`, `all`
- 其他：`rcp`（倒数）, `fmod`, `remainder`, `remquo`

## 关键实现逻辑

所有运算符和函数使用 friend 内联定义模式。这使得它们在类实例化时自动可用，且参与 ADL（参数依赖查找），无需显式命名空间限定。混合类型运算通过 `arithmetic_result_t` 自动提升结果类型。

## 依赖关系

- `<math/compiler.h>` -- 平台宏
- `<cmath>` -- 标准数学函数
