# Frustum.cpp

## 文件概述

`Frustum` 类的实现文件，提供视锥体的构建、平面提取、以及与 AABB/球体的相交测试和点包含测试。

## 核心类/结构体/函数

### `Frustum` 类方法

- **构造函数 `Frustum(const mat4f& pv)`** -- 从投影-视图矩阵构建视锥体。
- **`setProjection(const mat4f& pv)`** -- 从组合矩阵提取 6 个裁剪平面。
- **`getNormalizedPlane(Plane)`** -- 获取指定平面（归一化后）。
- **`getNormalizedPlanes(float4[6])`** -- 获取所有 6 个归一化平面。
- **`intersects(const Box&)`** -- 测试 AABB 是否与视锥体相交，委托给 `Culler`。
- **`intersects(const float4& sphere)`** -- 测试球体是否与视锥体相交，委托给 `Culler`。
- **`contains(float3 p)`** -- 测试点是否在视锥体内，返回到最近平面的有符号距离（负值表示在内部）。

## 关键实现逻辑

- **平面提取**：使用 Gribb & Hartmann 的方法从转置的投影-视图矩阵提取 6 个平面（左、右、下、上、近、远）。这等价于将裁剪空间的平面变换到视图空间。
- **平面归一化**：所有平面的法线被归一化，这对球体-视锥体测试是必需的（AABB 测试不需要）。
- **平面存储顺序**：`mPlanes[0-5]` 分别存储左、右、下、上、远、近平面。
- **`UTILS_NOINLINE`**：`setProjection` 标记为不内联，防止 LLVM 在构造函数中产生冗余的内联版本。
- **调试输出**：Debug 模式下支持 `operator<<` 流输出。

## 依赖关系

- `filament/Frustum.h` -- 公共头文件
- `Culler.h` -- 相交测试实现
- `math/mat4.h`、`math/vec3.h`、`math/vec4.h` -- 数学类型

## 被引用关系

被相机、视图和裁剪系统广泛使用。`Camera::getFrustum()` 返回视锥体，场景渲染时用于裁剪不可见物体。
