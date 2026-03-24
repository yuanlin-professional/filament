# surface_shading_model_cloth.fs

## 文件概述
布料着色模型实现。使用 Charlie NDF（法线分布函数）和 Neubelt 可见性函数替代标准 GGX，模拟织物的柔和镜面反射外观，可选次表面散射颜色。

## 核心功能
- Charlie NDF 提供更宽泛、柔和的镜面高光
- Neubelt 可见性函数专为布料设计
- 可选次表面散射颜色补偿能量损失

## 依赖关系
- `surface_brdf.fs` - BRDF 函数
- `surface_lighting.fs` - Light / PixelParams 结构体
