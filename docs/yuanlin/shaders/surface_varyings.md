# surface_varyings.glsl

## 文件概述
顶点-片段着色器间插值变量（varying）声明。定义了从顶点着色器传递到片段着色器的所有插值数据。

## 核心变量
- 世界空间位置和法线
- 纹理坐标（UV0/UV1）
- 切线空间（TBN 矩阵相关）
- 阴影坐标
- 顶点颜色
- 实例索引

## 依赖关系
- 由 `surface_main.vs` 写入
- 由 `surface_getters.fs` 和 `surface_shading_parameters.fs` 读取
