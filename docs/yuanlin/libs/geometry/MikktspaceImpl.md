# MikktspaceImpl - Mikktspace 切线空间计算实现

## 文件概述

内部实现类，封装了 Mikktspace 算法用于计算网格的切线空间（TBN）。该类作为 `TangentSpaceMesh` 的后端，在选择 `MIKKTSPACE` 算法时被调用。Mikktspace 算法会对网格进行重新索引（remesh），生成新的顶点和三角形索引。

**源文件**: `libs/geometry/src/MikktspaceImpl.h`, `libs/geometry/src/MikktspaceImpl.cpp`

## 核心类/结构体

### `MikktspaceImpl`
- **IOVertex**: 内部顶点结构，包含 `position`(float3)、`uv`(float2)、`tangentSpace`(quatf)
- **InputAttribute**: 描述输入辅助属性（UV1、颜色、骨骼关节、权重等）的数据指针、步幅和大小
- 成员变量存储面数、位置/法线/UV 数据指针及步幅、三角形索引，支持 16 位和 32 位索引

## 关键实现逻辑

1. **SMikkTSpaceInterface 回调**: 实现了 Mikktspace 库要求的静态回调函数（`getNumFaces`、`getPosition`、`getNormal`、`getTexCoord`、`setTSpaceBasic`），通过 `m_pUserData` 指针回溯到实例
2. **setTSpaceBasic**: 接收 Mikktspace 计算出的切线和符号，构建 TBN 矩阵，使用 `mat3f::packTangentFrame` 打包为四元数，并将位置、UV、TBN 及辅助属性写入输出缓冲区
3. **run()**: 调用 `genTangSpaceDefault` 执行 Mikktspace 算法，然后用 `meshopt_generateVertexRemap` 和 `meshopt_remapVertexBuffer/IndexBuffer` 去重并生成紧凑的顶点/索引数据
4. **辅助属性传递**: 在重新索引过程中，UV1、颜色、骨骼关节、权重等辅助属性一并被正确映射到新网格

## 依赖关系

- `TangentSpaceMeshInternal.h` - 内部数据结构（TangentSpaceMeshInput/Output）
- `<mikktspace/mikktspace.h>` - Mikktspace 算法库
- `<meshoptimizer.h>` - 顶点去重和索引优化
- `<math/quat.h>`, `<math/vec2.h>`, `<math/vec3.h>`, `<math/mat3.h>`, `<math/norm.h>` - 数学运算
- `<utils/Panic.h>` - 断言和错误处理
