# UibStructs.h

## 文件概述
定义 Filament 引擎所有 UBO（Uniform Buffer Object）的 C 结构体布局。这些结构体的内存布局严格遵循 GLSL std140 规范，直接映射到 GPU 着色器中的 uniform block。

## 核心类/结构体/函数
- **`std140::vec3/vec4/mat33/mat44`** - std140 对齐的数学类型辅助结构体
- **`PerViewUib`**（2KiB）- 每视图 uniform：视图/投影矩阵、时间、分辨率、AO、方向光、IBL、阴影、雾、SSR 等
- **`PerRenderableData`**（256B）- 每可渲染对象数据：模型矩阵、法线矩阵、变形目标数、标志位、对象 ID
- **`PerRenderableUib`** - 可渲染对象 UBO，包含 `CONFIG_MAX_INSTANCES` 个 `PerRenderableData`
- **`LightsUib`**（64B）- 灯光数据：位置、方向、颜色、强度、类型、阴影信息
- **`ShadowUib`** - 阴影数据：每个阴影贴图的 lightFromWorld 矩阵、偏置、VSM 参数等
- **`FroxelRecordUib` / `FroxelsUib`**（各 16KiB）- Froxel 聚类光照记录缓冲区
- **`PerRenderableBoneUib`** - 骨骼动画数据：每根骨骼 3x4 变换矩阵 + 余因子矩阵
- **`PerRenderableMorphingUib`** - 变形权重数组

## 关键实现逻辑
- 所有 UBO 大小受 `CONFIG_MINSPEC_UBO_SIZE`（16KiB）限制以确保 ES3.0 兼容
- `PerRenderableData::packFlagsChannels()` 将蒙皮、变形、接触阴影等标志打包到单个 int32
- `PerViewUib` 严格对齐到 2KiB（128 个 float4）

## 依赖关系
- `math/mat3.h`、`math/mat4.h`、`math/vec4.h`
- `private/filament/EngineEnums.h`
