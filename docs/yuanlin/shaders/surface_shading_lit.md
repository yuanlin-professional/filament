# surface_shading_lit.fs

## 文件概述
Lit 着色总入口。根据着色模型选择（Standard/Cloth/Subsurface）分发到对应的着色实现，是材质系统与具体着色模型之间的适配层。

## 核心功能
- 选择并调用合适的着色模型函数
- 处理自定义着色（`surface_shading_lit_custom.fs`）

## 依赖关系
- `surface_shading_model_standard.fs`
- `surface_shading_model_cloth.fs`
- `surface_shading_model_subsurface.fs`
- `surface_shading_lit_custom.fs`
