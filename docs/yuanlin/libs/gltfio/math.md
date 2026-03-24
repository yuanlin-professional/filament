# math

## 文件概述
gltfio 的数学工具头文件，提供 glTF 动画和变换所需的辅助函数，包括三次样条插值、矩阵分解/合成以及 UV 变换矩阵构建。

## 核心函数

- **`cubicSpline(vert0, tang0, vert1, tang1, t)`**: 模板函数，实现 Hermite 三次样条插值，用于 glTF 的 CUBICSPLINE 插值类型。公式：`s0*p0 + s1*m0*t + s2*p1 + s3*m1*t`。
- **`decomposeMatrix(mat, translation, rotation, scale)`**: 将 4x4 变换矩阵分解为平移、旋转（四元数）和缩放三个分量。通过列向量长度提取缩放，通过行列式符号处理镜像变换，最后从去缩放矩阵提取旋转。
- **`composeMatrix(translation, rotation, scale)`**: 从 TRS 分量合成 4x4 变换矩阵，直接展开四元数到旋转矩阵的公式并乘以缩放。
- **`matrixFromUvTransform(offset, rotation, scale)`**: 根据 glTF 的 KHR_texture_transform 扩展参数构建 3x3 UV 变换矩阵。

## 依赖关系
- `math/quat.h`, `math/vec3.h`, `math/mat3.h`, `math/mat4.h`, `math/TVecHelpers.h`
- `utils/compiler.h`
