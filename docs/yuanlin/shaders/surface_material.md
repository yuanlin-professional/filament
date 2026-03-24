# surface_material.fs

## 文件概述
材质接口层。定义 `MaterialInputs` 结构体的初始化和默认值设置，是用户材质函数 `material()` 的数据容器。

## 核心功能
- `MaterialInputs` 结构体初始化（默认 baseColor、roughness、metallic 等）
- 为各着色模型（Standard/Cloth/Subsurface）设置合适的默认参数

## 依赖关系
- `surface_material_inputs.fs` - MaterialInputs 结构体定义
