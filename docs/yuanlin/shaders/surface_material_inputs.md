# surface_material_inputs.fs / surface_material_inputs.vs

## 文件概述
材质输入结构体定义（片段/顶点着色器版本）。声明了用户可通过 `material()` 函数设置的所有材质属性。

## 核心结构体（片段着色器）
- `MaterialInputs` - 包含 baseColor、metallic、roughness、reflectance、ambientOcclusion、emissive 等标准 PBR 属性
- 可选属性：normal、bentNormal、clearCoat、anisotropy、subsurfaceColor、sheenColor、absorption 等

## 核心结构体（顶点着色器）
- `MaterialVertexInputs` - 包含顶点位置偏移和自定义属性

## 依赖关系
- 由 `surface_material.fs` 初始化
- 被 `surface_main.fs` 传递给光照系统
