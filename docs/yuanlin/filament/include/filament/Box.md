# Box.h

## 文件概述

定义轴对齐包围盒 (AABB) 的两种表示形式：`Box`（中心+半范围）和 `Aabb`（最小/最大坐标），用于碰撞检测、视锥体剔除等几何计算。

## 核心类/结构体

- **`Box`** - 以中心点和半范围表示的轴对齐包围盒，常用于视锥体剔除。
- **`Aabb`** - 以最小/最大坐标表示的轴对齐包围盒，提供角点枚举和包含测试。
- **`Aabb::Corners`** - 存储 AABB 8 个角点的辅助结构体。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Box::isEmpty()` | 判断盒子是否为空（范围为零） |
| `Box::getMin() / getMax()` | 获取最小/最大角点坐标 |
| `Box::set(min, max)` | 通过最小/最大坐标初始化盒子 |
| `Box::unionSelf(box)` | 计算与另一个盒子的并集包围盒 |
| `Box::translateTo(tr)` | 将盒子平移到指定中心位置 |
| `Box::getBoundingSphere()` | 计算最小包围球（返回 float4：xyz=中心, w=半径） |
| `Box::transform(m, t, box)` | 对盒子做线性变换和平移 |
| `Aabb::center() / extent()` | 计算中心点和半范围 |
| `Aabb::getCorners()` | 返回 8 个角点 |
| `Aabb::contains(p)` | 测试点是否在盒子内（返回最大有符号距离） |
| `Aabb::transform(m)` | 对 AABB 做仿射变换 |

## 依赖关系

- `utils/compiler.h`
- `math/mat3.h`, `math/mat4.h`, `math/vec3.h`, `math/vec4.h`
- `<float.h>`, `<stddef.h>`

## 使用示例

```cpp
filament::Box box;
box.set({-1, -1, -1}, {1, 1, 1});

// 获取包围球
auto sphere = box.getBoundingSphere(); // center + radius

// 合并两个盒子
filament::Box other;
other.set({0, 0, 0}, {2, 2, 2});
box.unionSelf(other);

// AABB 包含测试
filament::Aabb aabb{{-1, -1, -1}, {1, 1, 1}};
float dist = aabb.contains({0.5f, 0.5f, 0.5f}); // 负值表示在内部
```
