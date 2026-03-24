# surface_depth_main.fs

## 文件概述
深度通道片段着色器。用于阴影贴图生成和深度预通道（depth prepass），仅计算深度值，不进行完整光照。

## 核心功能
- 对遮罩混合模式执行 alpha 测试
- VSM（方差阴影贴图）模式下输出深度矩和 EVSM 指数加权
- 拾取模式（picking）下输出对象 ID

## 依赖关系
- `surface_material.fs` - 获取 alpha 值用于遮罩测试
