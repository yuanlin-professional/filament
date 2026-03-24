# mathfwd.h - 数学类型前向声明

## 文件概述

提供 Filament 数学库所有核心类型的前向声明和类型别名，允许头文件在不 include 完整定义的情况下引用数学类型，减少编译依赖。

## 核心定义

### MSVC 特殊处理
由于 MSVC 无法正确计算前向声明类型的大小（见 github issue #2190），在 MSVC 下直接包含所有完整头文件。

### 前向声明（非 MSVC 平台）
在 `filament::math::details` 命名空间下前向声明：
- `TVec2<T>`, `TVec3<T>`, `TVec4<T>` -- 向量模板
- `TMat22<T>`, `TMat33<T>`, `TMat44<T>` -- 矩阵模板
- `TQuaternion<T>` -- 四元数模板

### 类型别名
在 `filament::math` 命名空间下定义完整的类型别名集合：
- 向量：`float2/3/4`, `double2/3/4`, `int2/3/4`, `uint2/3/4`, `short2/3/4`, `ushort2/3/4`, `byte2/3/4`, `ubyte2/3/4`, `bool2/3/4`
- 矩阵：`mat2/mat2f`, `mat3/mat3f`, `mat4/mat4f`
- 四元数：`quat`, `quatf`

## 依赖关系

- MSVC 下：`<math/vec2.h>` ~ `<math/mat4.h>` 全部包含
- 非 MSVC：仅 `<stdint.h>`
