# SurfaceOrientation - 表面朝向计算工具

## 文件概述

提供将网格表面法线、切线等数据转换为 Filament 风格 TANGENTS 缓冲区（四元数表示）的功能。通过 Builder 模式接受不同输入组合，选择最合适的算法生成切线空间四元数。此类已被 `TangentSpaceMesh` 取代，但仍保留使用。

**源文件**: `libs/geometry/include/geometry/SurfaceOrientation.h`, `libs/geometry/src/SurfaceOrientation.cpp`

## 核心类/结构体

### `SurfaceOrientation::Builder`
接受以下输入组合之一：
1. **仅法线** - 使用 Frisvad 方法选取任意切线方向（不推荐）
2. **法线 + 切线** - 根据 W 分量符号确定副切线方向
3. **法线 + UV + 位置 + 索引** - 使用 Lengyel 方法
4. **位置 + 索引** - 生成平面着色的法线

### `SurfaceOrientation`
不可变对象，存储计算好的四元数数组。通过 `getQuats()` 方法输出为 `quatf`、`short4`(SNorm16) 或 `quath`(半精度) 格式。

### `OrientationBuilderImpl` / `OrientationImpl`
内部实现结构体，分别保存构建参数和计算结果（四元数向量）。

## 关键实现逻辑

1. **Frisvad 方法** (`buildWithNormalsOnly`): 基于 Frisvad 2012 论文，从单位法线向量构建正交基，处理了 Y 轴方向奇异点（N.y 接近 -1 时）
2. **已提供切线** (`buildWithSuppliedTangents`): 使用 Gram-Schmidt 正交化确保切线与法线正交，优先保留法线精度
3. **Lengyel 方法** (`buildWithUvs`): 基于 UV 和位置差异计算切线/副切线方向，累加到各三角形顶点后做 Gram-Schmidt 正交化，处理 UV 退化情况（除零保护）
4. **平面着色** (`buildWithFlatNormals`): 从三角形面法线生成，再委托给 `buildWithNormalsOnly`
5. 所有方法最终使用 `mat3f::packTangentFrame` 将 TBN 矩阵打包为四元数

## 依赖关系

- `<math/quat.h>`, `<math/vec3.h>`, `<math/vec4.h>`, `<math/mat3.h>`, `<math/norm.h>` - 数学运算
- `<utils/compiler.h>`, `<utils/Panic.h>`, `<utils/debug.h>` - 工具和断言
