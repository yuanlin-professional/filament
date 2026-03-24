# common_math.glsl

## 文件概述
通用数学工具定义。提供 PI 常量、浮点精度限制、`saturate` 宏以及移动端除零保护等基础数学设施。

## 核心定义
- `PI` / `HALF_PI` - 数学常量
- `MEDIUMP_FLT_MAX` / `MEDIUMP_FLT_MIN` - mediump 精度范围
- `FLT_EPS` - 浮点 epsilon（移动端使用 mediump 最小值，桌面端使用 1e-5）
- `saturate(x)` - clamp 到 [0, 1] 范围
- `PREVENT_DIV0(n, d, magic)` - 移动端除零保护（桌面端直接除法）

## 关键实现逻辑
- 标量运算函数：`sq()`、`pow5()`、`max3()`、`mulMat4x4Float3()` 等
- 移动端精度适配通过宏切换实现

## 依赖关系
- 被所有着色器文件使用
