# half.h - 半精度浮点类型

## 文件概述

定义了 16 位半精度浮点类型 `half`（IEEE 754 binary16）。在 ARM NEON 平台上直接使用硬件 `__fp16` 类型；其他平台通过模板类 `fp<S,E,M>` 实现软件模拟。广泛用于 GPU 数据传输和节省内存的场景。

## 核心类/结构体

### `fp<S, E, M>` 模板类
通用的自定义浮点格式类，模板参数 S=符号位数、E=指数位数、M=尾数位数（总位宽不超过 16 位）。
- `fromf(float f)` -- 将 float 转换为自定义浮点格式，支持 INF/NaN 处理、舍入
- `tof(fp in)` -- 将自定义浮点格式转换回 float
- 内部使用 `fp32` 辅助结构进行 IEEE 754 位操作

### `half` 类型
- ARM NEON 平台：`using half = __fp16;` 直接使用硬件类型
- 其他平台：`class half` 包装 `fp<1, 5, 10>`，提供 `float` 隐式转换
- `getBits(half)` / `makeHalf(uint16_t)` -- 底层位操作辅助函数
- 用户字面量：`1.0_h` 创建 half 值

### `std::numeric_limits<half>` 特化
提供 half 类型的数值极限，包括：最小/最大值、epsilon、infinity、quiet_NaN 等。

## 关键实现逻辑

`fromf` 的转换策略：先处理 INF/NaN 特殊值；对正常值，通过清除多余尾数位、加舍入偏移、乘以魔数调整指数偏移量来完成 float32 到 float16 的转换。使用位操作而非条件分支，性能友好。

half 的数值范围：最小正规数 `6.10e-5`，最大值 `65504`，可精确表示 0~2048 的整数。

## 依赖关系

- `<math/compiler.h>` -- `is_arithmetic`, `is_floating_point` 特化
- `<limits>`, `<type_traits>`, `<stdint.h>`
