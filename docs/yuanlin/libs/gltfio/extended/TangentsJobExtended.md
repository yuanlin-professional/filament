# TangentsJobExtended

## 文件概述
TangentsJobExtended 是 TangentsJob 的扩展版本，使用 TangentSpaceMesh（支持 mikktspace 算法）进行切线空间计算。与标准版本不同，它可能改变网格的顶点数、索引数和三角形数，因此需要输出完整的重网格化数据。

## 核心结构体

- **`TangentsJobExtended::InputParams`**: 输入参数，包含 cgltf_primitive、变形目标索引和 UvMap。
- **`TangentsJobExtended::OutputParams`**: 输出参数，包含：
  - 三角形数据（triangleCount + uint3 数组）
  - 顶点数据（vertexCount + 各属性数组：tbn/uv0/uv1/positions/joints/weights/colors）
  - `isEmpty()` 方法检查是否有输出数据
- **`TangentsJobExtended::Params`**: 捆绑输入、输出和作业类型标志（VERTEX_JOB/INDEX_JOB/MORPH_TARGET_JOB）。

## 关键实现逻辑

- `run()` 从 cgltf primitive 提取所有顶点属性（位置、法线、切线、UV、关节、权重、颜色），传递给 TangentSpaceMeshWrapper。
- TangentSpaceMeshWrapper 内部可能使用 mikktspace 算法重新生成切线空间，这可能导致顶点分裂和索引重排。
- 输出所有经过重网格化的顶点属性和索引数据，供 AssetLoaderExtended 构建 VertexBuffer/IndexBuffer。

## 依赖关系
- `gltfio/MaterialProvider.h`（UvMap）
- `AssetLoaderExtended.h`, `TangentSpaceMeshWrapper.h`
- `GltfEnums.h`, `FFilamentAsset.h`, `Utility.h`
- `geometry/TangentSpaceMesh.h`
- `cgltf.h`
- `math/vec4.h`
