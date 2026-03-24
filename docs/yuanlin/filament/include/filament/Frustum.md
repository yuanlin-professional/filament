# Frustum.h

## 文件概述

定义由六个平面构成的视锥体，用于视锥体剔除（判断盒子、球体和点是否在视锥体内）。

## 核心类/结构体

- **`Frustum`** - 视锥体类，由六个平面（左/右/下/上/远/近）定义。
- **`Frustum::Plane`** - 枚举：`LEFT`/`RIGHT`/`BOTTOM`/`TOP`/`FAR`/`NEAR`。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Frustum(pv)` | 从 GL 投影矩阵构造视锥体 |
| `setProjection(pv)` | 从投影矩阵更新视锥体 |
| `getNormalizedPlane(plane)` | 获取指定平面的方程（归一化法线） |
| `getNormalizedPlanes()` | 获取所有六个平面方程 |
| `intersects(box)` | 测试盒子是否与视锥体相交（可见性测试） |
| `intersects(sphere)` | 测试球体是否与视锥体相交 |
| `contains(p)` | 测试点是否在视锥体内（返回有符号距离） |

## 依赖关系

- `utils/compiler.h`
- `math/mat4.h`, `math/vec3.h`, `math/vec4.h`
