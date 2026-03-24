# TangentSpaceMesh - 切线空间网格构建器

## 文件概述

`TangentSpaceMesh` 是 Filament 中用于构建切线空间（TBN）缓冲区的核心类，取代了旧的 `SurfaceOrientation`。支持多种算法（Mikktspace、Lengyel、Frisvad、Hughes-Moller、平面着色），并能在重新网格化时正确传递辅助顶点属性（UV1、颜色、骨骼关节、权重）。

**源文件**: `libs/geometry/include/geometry/TangentSpaceMesh.h`, `libs/geometry/src/TangentSpaceMesh.cpp`, `libs/geometry/src/TangentSpaceMeshInternal.h`

## 核心类/结构体

### `TangentSpaceMesh`
- **Algorithm 枚举**: `DEFAULT`（自动选择）、`MIKKTSPACE`、`LENGYEL`、`HUGHES_MOLLER`、`FRISVAD`
- **AuxAttribute 枚举**: `UV1`、`COLORS`、`JOINTS`、`WEIGHTS` - 在重新网格化时需要一并映射的辅助属性
- **Builder**: 设置顶点数、法线、切线、UV、位置、三角形、算法提示，调用 `build()` 生成结果
- 输出方法: `getVertexCount()`、`getPositions()`、`getUVs()`、`getQuats()`(三种精度)、`getTriangles()`、`getAux()`、`remeshed()`

### `TangentSpaceMeshInput` (内部)
存储所有输入属性的统一映射（`AttributeMap`），使用 `std::variant` 支持多种数据类型。提供便捷的访问方法（`positions()`、`normals()`、`uvs()` 等）。

### `TangentSpaceMeshOutput` (内部)
通过 `InternalArray<T>` 管理输出数据，支持"借用"（零拷贝引用输入）和"分配"两种模式。

### `InternalArray<T>` (内部)
智能数组模板，可以借用外部指针（零拷贝）或自行分配存储，统一访问接口。

## 关键实现逻辑

1. **算法选择** (`selectAlgorithm`): 根据输入数据的可用性和用户提示自动选择最佳算法。DEFAULT 模式下：有法线+UV+位置+索引选 MIKKTSPACE，有法线+切线选 TANGENTS_PROVIDED，仅位置+索引选 FLAT_SHADING，仅法线选 FRISVAD
2. **Frisvad 方法**: 仅需法线，从法线构建正交基。不重新网格化，直接借用输入数据
3. **Hughes-Moller 方法**: 仅需法线，根据法线分量大小选择初始切线方向。不重新网格化
4. **Lengyel 方法**: 基于 UV 梯度计算切线/副切线，Gram-Schmidt 正交化。不重新网格化
5. **Mikktspace 方法**: 委托给 `MikktspaceImpl`，会重新网格化
6. **平面着色方法**: 拆分三角形为独立顶点（每个三角形3个新顶点），从面法线用 Frisvad 方法生成切线空间。会重新网格化
7. **已提供切线方法**: 从法线和切线(含 W 符号)重建 TBN，强制正交化

## 依赖关系

- `MikktspaceImpl.h` - Mikktspace 算法实现
- `TangentSpaceMeshInternal.h` - 内部数据结构定义（输入/输出/算法枚举/属性枚举）
- `<math/quat.h>`, `<math/vec3.h>`, `<math/vec4.h>`, `<math/mat3.h>`, `<math/norm.h>` - 数学运算
- `<utils/Log.h>`, `<utils/Panic.h>`, `<utils/debug.h>` - 日志和断言
