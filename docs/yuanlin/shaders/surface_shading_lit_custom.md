# surface_shading_lit_custom.fs

## 文件概述
自定义 Lit 着色扩展点。允许用户在材质定义中提供自定义的着色函数，替换或扩展标准着色模型的光照计算。

## 核心功能
- 提供用户自定义着色函数的挂载点
- 在标准着色流程中可选调用

## 依赖关系
- `surface_lighting.fs` - Light / PixelParams 结构体
