# compiler.h - 编译器兼容性与平台适配

## 文件概述

该文件为 Filament 数学库提供跨编译器/跨平台的兼容性宏定义和类型萃取工具。主要解决 MSVC、Clang、GCC 之间的差异，包括分支预测提示、属性标注、constexpr 行为差异等。

## 核心定义

### 平台宏
- `MATH_LIKELY(exp)` / `MATH_UNLIKELY(exp)` -- 分支预测提示，基于 `__builtin_expect`
- `MATH_UNUSED` -- 标记未使用变量，避免编译器警告
- `MATH_PURE` -- 标记纯函数（无副作用），帮助编译器优化
- `MATH_EMPTY_BASES` -- MSVC 的空基类优化（`__declspec(empty_bases)`）
- `MATH_NOUNROLL` -- 禁止循环展开的 pragma
- `MAKE_CONSTEXPR(e)` -- 包装 `__builtin_constant_p`，MSVC 下直接返回表达式

### MSVC 特殊处理
- `MATH_CONSTEXPR_INIT` -- MSVC 下为 `{}`（零初始化），其他平台为空
- `MATH_DEFAULT_CTOR` -- MSVC 下为 `{}`，其他平台为 `= default;`
- `CONSTEXPR_IF_NOT_MSVC` -- MSVC 下为空，其他平台为 `constexpr`
- 取消 Windows 头文件中 `max`、`min`、`far`、`near` 等宏定义

### 类型萃取
- `filament::math::is_arithmetic<T>` -- 自定义的算术类型判断，替代 `std::is_arithmetic`（为支持 `half` 类型特化）
- `filament::math::is_floating_point<T>` -- 自定义浮点类型判断

## 关键实现逻辑

为规避 Clang 20 对 `std::is_arithmetic` 特化的限制以及 MSVC 2019 的兼容问题，在 `filament::math` 命名空间下定义了独立的 `is_arithmetic` 和 `is_floating_point` 类型萃取，后续在 `half.h` 中对 `half` 类型进行特化。

## 依赖关系

- `<type_traits>` -- 标准库类型萃取
