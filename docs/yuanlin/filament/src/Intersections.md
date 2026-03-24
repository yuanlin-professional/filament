# Intersections.h

## 文件概述

提供一组用于 Froxel 化光照系统的几何相交测试工具函数，包括球体-平面相交、球体-锥体相交和三平面交点计算。所有函数都是 `inline constexpr`，可以在编译期或运行时高效使用。

## 核心类/结构体/函数

### 球体-平面相交

- **`spherePlaneIntersection(float4 s, float4 p)`** -- 计算球体与归一化平面的相交。返回的 `float4` 中 `.xyz` 是相交圆的圆心，`.w` 是半径的平方（`<= 0` 表示无交集）。输入球体的 `.w` 是半径的平方。
- **`spherePlaneIntersection(float4 s, float pw)`** -- 简化版本，平面为 `{0, 0, 1, pw}` 形式。

### 球体-锥体相交

- **`sphereConeIntersectionFast(sphere, conePosition, coneAxis, coneSinInverse, coneCosSquared)`** -- 快速球体-锥体相交测试（可能有误报）。无分支实现，适合 SIMD 向量化。
- **`sphereConeIntersection(...)`** -- 精确球体-锥体相交测试，在快速版本基础上增加了近顶点区域的精确检查。

### 三平面交点

- **`planeIntersection(float4 p0, float4 p1, float4 p2)`** -- 计算三个平面的交点。使用克莱姆法则（通过叉积和点积）求解。

## 关键实现逻辑

- **球体-平面相交**：通过计算球心到平面的有符号距离 `d`，将球心沿法线方向投影到平面上，并计算相交圆的半径平方 `r^2 = R^2 - d^2`。返回值同时包含新的中心和半径信息，支持链式相交（先 Z 平面，再 Y 平面，再 X 平面）。
- **球体-锥体快速测试**：将球体沿锥体轴线方向扩展 `r/sin(angle)`，然后检查扩展后的球心是否在锥体内。使用 `e*e >= dd*cosSquared && e > 0` 的无分支条件判断。
- **constexpr 优化**：所有函数标记为 `constexpr`，允许编译器在常量参数时进行编译期求值。

## 依赖关系

- `math/mat4.h`、`math/vec4.h` -- 数学类型
- `utils/compiler.h` -- 编译器工具

## 被引用关系

主要被 `Froxelizer.cpp` 使用，是 Froxel 化光照分配算法中球体-Froxel 相交测试的核心计算。`planeIntersection` 用于计算 Froxel 包围球。
