# TangentSpaceMeshWrapper

## 文件概述
TangentSpaceMeshWrapper 是 geometry::TangentSpaceMesh 的包装器。对于无光照（unlit）材质，它直接透传输入数据而不进行 TSM 变换；对于有光照材质，则委托给 TangentSpaceMesh 执行 mikktspace 切线空间计算。

## 核心类/结构体

- **`TangentSpaceMeshWrapper`**: 提供与 TangentSpaceMesh 类似的接口：
  - `getPositions()`, `getUVs()`, `getQuats()`, `getTriangles()`: 获取输出数据
  - `getVertexCount()`, `getTriangleCount()`: 获取输出尺寸
  - `getAux()`: 获取辅助属性（joints、weights、colors 等）
  - `destroy()`: 静态销毁方法
- **`TangentSpaceMeshWrapper::Builder`**: 构建器模式，接受输入数据：
  - `vertexCount()`, `triangleCount()`
  - `positions()`, `normals()`, `tangents()`, `uvs()`, `triangles()`
  - `aux()`: 模板方法，支持 float2/float3/float4/ushort3/ushort4 类型
  - `build()`: 构建并返回 TangentSpaceMeshWrapper

## 关键实现逻辑

- **双模式实现**: 内部使用 `Passthrough`（直通模式）或 `geometry::TangentSpaceMesh`（计算模式）：
  - Unlit 材质：使用 PassthroughMesh，直接复制输入数据到输出。顶点和三角形数量不变。
  - Lit 材质：使用 TangentSpaceMesh，可能改变顶点/三角形数量（因切线空间分裂）。
- 辅助属性通过 AuxType 枚举进行类型安全的存取。

## 依赖关系
- `geometry/TangentSpaceMesh.h`
- `math/vec4.h`
