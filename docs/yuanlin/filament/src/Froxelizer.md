# Froxelizer.h / Froxelizer.cpp

## 文件概述

实现 Froxel（frustum + voxel）化光照系统。将视锥体空间划分为三维网格（Froxels），然后将场景中的点光源和聚光灯分配到对应的 Froxel 中。这是 Filament 集群化前向渲染（Clustered Forward Rendering）的核心组件，允许着色器高效地查找影响每个像素的光源列表。

## 核心类/结构体/函数

### `Froxel` 类

表示单个 Froxel（6 个平面定义的截锥体子体积）。

### `Froxelizer` 类

- **`Froxelizer(FEngine&)`** -- 构造函数，创建 GPU 缓冲区。
- **`prepare(...)`** -- 每帧准备，分配临时内存，设置视口和投影参数。返回是否需要更新 Uniform。
- **`froxelizeLights(engine, viewMatrix, lightData)`** -- 核心方法，将光源分配到 Froxels 中。线程安全。
- **`commit(DriverApi&)`** -- 将 Froxel 数据上传到 GPU。
- **`updateUniforms(PerViewUib&)`** -- 更新着色器 Uniform 参数。
- **`setOptions(zLightNear, zLightFar)`** -- 设置光照的近远平面。

### 内部结构

- **`FroxelEntry`** -- GPU 端的 Froxel 条目（4 字节：offset + count）。
- **`LightRecord`** -- 每个 Froxel 的光源位集合。
- **`LightParams`** -- 光源参数（位置、方向、半径、锥角等）。
- **`LightTreeNode`** -- 光源二叉树节点，用于加速 z 范围查询。
- **`FroxelThreadData`** -- 每线程的 Froxel 数据，用于并行光照分配。

## 关键实现逻辑

- **Froxel 网格划分**：`computeFroxelLayout` 根据视口大小和缓冲区限制计算 Froxel 的 X/Y/Z 维度。X-Y 方向使用正方形 Froxel，Z 方向使用对数分布（16 个切片）。
- **光照分配并行化**：`froxelizeLoop` 将光源按 GROUP_COUNT 分组，使用 JobSystem 并行处理。每个作业处理一组光源对所有 Froxel 的影响。
- **球体-平面相交测试**：`froxelizePointAndSpotLight` 通过逐维度球体-平面相交缩小搜索范围，先 Z 维度，再 Y 维度，最后 X 维度。聚光灯额外进行球体-锥体相交测试。
- **记录压缩**：`froxelizeAssignRecordsCompress` 将光源位集合转换为紧凑的记录列表，相邻或上方相同记录的 Froxel 可以共享同一条记录，节省 10%+ 的记录空间。
- **包围球优化**：为每个 Froxel 预计算包围球，加速聚光灯的锥体相交测试。
- **UBO 限制**：Froxel 缓冲区和记录缓冲区受 UBO 大小限制（通常 16KB），决定了最大 Froxel 数量和光源记录数量。

## 依赖关系

- `Allocators.h` -- 帧内存分配器
- `Intersections.h` -- 球体/平面/锥体相交测试
- `details/Scene.h`、`details/Engine.h` -- 场景和引擎内部实现
- `components/LightManager.h` -- 光源管理器
- `private/filament/EngineEnums.h` -- 配置常量（如 `CONFIG_MAX_LIGHT_COUNT`）
- `utils/BinaryTreeArray.h`、`utils/bitset.h`、`utils/JobSystem.h` -- 工具类

## 被引用关系

由 `FView` 在每帧渲染前调用 `prepare` 和 `froxelizeLights`，生成的数据通过 Uniform Buffer 传递给着色器。着色器在片段阶段读取 Froxel 数据来查找影响当前像素的光源。
