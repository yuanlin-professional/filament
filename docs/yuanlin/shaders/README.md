# 着色器库 (`shaders/`)

## 模块概述

`shaders/` 目录包含 Filament 渲染引擎的核心着色器代码库。这些 GLSL 着色器片段 (shader chunks) 构成了 Filament 材质系统的基础，涵盖表面着色、光照计算、后处理等渲染管线的各个阶段。着色器在构建时通过 `glslminifier` 工具压缩，再由 `resgen` 打包为 C++ 可嵌入的静态资源库 `libshaders`。

## 目录结构

```
shaders/
├── CMakeLists.txt              # CMake 构建脚本
└── src/
    ├── common_defines.glsl     # 全局宏定义和常量
    ├── common_getters.glsl     # 通用数据访问函数
    ├── common_math.glsl        # 数学工具函数
    ├── common_graphics.fs      # 图形通用函数
    ├── common_shading.fs       # 通用着色函数
    ├── surface_main.vs         # 表面顶点着色器主入口
    ├── surface_main.fs         # 表面片段着色器主入口
    ├── surface_main.cs         # 表面计算着色器主入口
    ├── surface_lighting.fs     # 光照总调度
    ├── surface_light_directional.fs  # 平行光计算
    ├── surface_light_punctual.fs     # 点光源/聚光灯计算
    ├── surface_light_indirect.fs     # 间接光照 (IBL)
    ├── surface_light_reflections.fs  # 屏幕空间反射
    ├── surface_brdf.fs         # BRDF 函数实现
    ├── surface_shading_lit.fs          # 标准光照着色
    ├── surface_shading_unlit.fs        # 无光照着色
    ├── surface_shading_model_standard.fs  # 标准着色模型
    ├── surface_shading_model_cloth.fs     # 布料着色模型
    ├── surface_shading_model_subsurface.fs # 次表面散射模型
    ├── surface_material.fs     # 材质参数处理
    ├── surface_fog.fs          # 雾效
    ├── surface_ambient_occlusion.fs  # 环境光遮蔽
    ├── surface_depth_main.fs   # 深度 Pass 入口
    ├── surface_getters.vs/fs/cs    # 各阶段数据获取
    ├── surface_instancing.glsl     # GPU 实例化
    ├── post_process_main.vs/fs     # 后处理主入口
    ├── post_process_inputs.vs/fs   # 后处理输入
    ├── post_process_getters.vs     # 后处理数据获取
    ├── inline_dithering.fs         # 内联抖动
    └── inline_vignette.fs          # 内联暗角
```

## 核心功能

- **PBR 着色管线**: 实现完整的基于物理的渲染 (PBR) 着色，支持标准、布料、次表面散射三种着色模型
- **多光源支持**: 平行光 (方向光)、点光源、聚光灯、IBL 间接光照
- **BRDF 实现**: 包含 Cook-Torrance 镜面反射和 Lambertian 漫反射 BRDF
- **后处理框架**: 顶点/片段着色器的后处理 Pass 入口和输入定义
- **构建时压缩**: 使用 `glslminifier` 在构建期自动压缩着色器代码
- **资源嵌入**: 通过 `resgen` 将着色器打包为 C++ 静态库，零运行时文件 I/O

## 依赖关系

| 依赖模块 | 说明 |
|---------|------|
| `tools/glslminifier` | GLSL 着色器代码压缩工具 |
| `tools/resgen` | 资源打包生成器，将着色器嵌入 C++ 静态库 |
| `filament/` | 引擎在编译材质时引用这些着色器片段 |
| `libs/filabridge/` | 着色器与引擎之间的桥接层 |

## 关键文件说明

| 文件 | 说明 |
|-----|------|
| `CMakeLists.txt` | 定义着色器压缩和打包管线: 源码 -> glslminifier 压缩 -> resgen 打包 -> libshaders.a |
| `src/surface_main.fs` | 表面着色的片段着色器主入口，调度材质计算和光照计算 |
| `src/surface_lighting.fs` | 光照计算的总调度器，协调直接光和间接光的计算流程 |
| `src/surface_brdf.fs` | BRDF (双向反射分布函数) 的核心实现，是 PBR 着色的数学基础 |
| `src/surface_shading_model_standard.fs` | 标准着色模型实现，适用于大多数常见材质 |
| `src/common_defines.glsl` | 全局着色器宏定义和编译期常量 |
| `src/post_process_main.fs` | 后处理片段着色器入口，用于色调映射、抗锯齿等后处理效果 |
