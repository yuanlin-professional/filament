# surface_types.glsl

## 文件概述
表面着色器数据类型定义。声明了阴影数据、骨骼数据和每渲染对象数据等关键结构体，以及相关的标志位常量。

## 核心结构体
- `ShadowData` - 阴影贴图参数：光源变换矩阵、裁剪区域、法线偏移、VSM 指数等
- `BoneData` - 骨骼蒙皮变换：3x4 变换矩阵 + 辅因子矩阵
- `PerRenderableData` - 每个渲染对象的数据：模型矩阵、法线矩阵、形态目标数量、标志位、对象 ID

## 核心常量
- `FILAMENT_OBJECT_SKINNING_ENABLED_BIT` (0x100) - 蒙皮启用
- `FILAMENT_OBJECT_MORPHING_*_BIT` - 形态目标标志（位置/切线/自定义）
- `FILAMENT_OBJECT_CONTACT_SHADOWS_BIT` (0x1000) - 接触阴影
- `FILAMENT_OBJECT_INSTANCE_BUFFER_BIT` (0x2000) - 实例缓冲区

## 依赖关系
- 被 `surface_getters.*` 和 `surface_main.*` 使用
