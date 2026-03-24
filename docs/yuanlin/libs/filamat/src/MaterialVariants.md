# MaterialVariants.h / MaterialVariants.cpp

## 文件概述
确定材质需要编译的所有着色器变体（Variant）列表。根据材质域（Surface/PostProcess/Compute）和用户过滤掩码生成变体集合。

## 核心类/结构体/函数
- **`filamat::Variant`** - 编译变体结构，包含 `filament::Variant`（变体位掩码）和 `Stage`（着色器阶段）
- **`determineSurfaceVariants()`** - 生成表面材质变体列表（约 96 个有效变体，按顶点/片段分开）
- **`determinePostProcessVariants()`** - 生成后处理变体列表（2 个：OPAQUE 和 TRANSLUCENT，各含顶点+片段）
- **`determineComputeVariants()`** - 生成计算着色器变体列表（仅 1 个）

## 关键实现逻辑
`determineSurfaceVariants()` 遍历所有 256 种变体，过滤掉保留的、用户屏蔽的、以及非光照材质不需要的变体，对每个有效变体分别生成顶点和片段着色器条目。

## 依赖关系
- `private/filament/Variant.h`、`private/filament/EngineEnums.h`
- `filament/MaterialEnums.h`
- `backend/DriverEnums.h`
