# Culler.h / Culler.cpp

## 文件概述

实现视锥体裁剪（Frustum Culling）功能。提供批量 AABB（轴对齐包围盒）和球体与视锥体的相交测试，以及单个 AABB/球体的测试接口。批量操作针对 SIMD 向量化做了优化。

## 核心类/结构体/函数

### `Culler` 类

- **`MODULO`** -- 常量值 8，所有批量操作的缓冲区大小必须是该值的倍数，以便 SIMD 向量化。
- **`round(size_t count)`** -- 将数量向上对齐到 `MODULO` 的倍数。
- **`intersects(results, frustum, center, extent, count, bit)`** -- 批量测试 AABB 数组与视锥体的相交。`bit` 参数指定结果中使用的位。
- **`intersects(results, frustum, spheres, count)`** -- 批量测试球体数组与视锥体的相交。
- **`intersects(frustum, Box)`** -- 单个 AABB 测试。
- **`intersects(frustum, float4 sphere)`** -- 单个球体测试。
- **`Test`** -- 用于测试/调试的辅助结构体。

## 关键实现逻辑

- **SIMD 向量化**：通过 `#pragma clang loop vectorize_width(4)` 指示编译器以 4 宽度向量化内层循环。对每个物体，手动展开 6 个平面的测试循环。
- **平面-球体测试**：计算球心到平面的有符号距离减去半径，使用 `fast::signbit` 替代分支判断。
- **平面-AABB 测试**：对每个平面计算 AABB 中心到平面的投影距离减去半径（使用绝对值分量乘以半扩展量），实现无分支的包含/排除判断。
- **位掩码结果**：结果使用 `uint8_t` 位掩码，`bit` 参数控制写入位的位置，支持多组裁剪结果的组合。
- **单物体适配**：单个 AABB/球体测试通过填充到 `MODULO` 大小的数组来复用批量测试路径。

## 依赖关系

- `filament/Frustum.h` -- 视锥体定义
- `filament/Box.h` -- AABB 包围盒定义
- `math/fast.h` -- 快速数学函数（`signbit`）
- `utils/compiler.h`、`utils/Slice.h` -- 工具

## 被引用关系

被 `Frustum.cpp` 直接调用，也被场景渲染管线中的裁剪阶段使用，用于快速剔除不在视锥体内的可渲染对象和光源。
