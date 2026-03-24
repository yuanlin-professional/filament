# surface_main.fs

## 文件概述
表面材质片段着色器主入口。协调材质输入初始化、alpha 遮罩、光照评估、后处理混合和最终颜色输出的完整流程。

## 核心函数
- `main()` - 片段着色器入口
- `blendPostLightingColor()` - 后光照颜色混合（支持 Opaque/Transparent/Add/Multiply/Screen 模式）
- `applyAlphaMask()` - Alpha 遮罩/裁剪（使用导数锐化边缘 + alpha to coverage）

## 关键实现逻辑
1. 初始化 LOD 偏移、实例索引和视口坐标
2. 调用 `initMaterial()` 初始化材质参数
3. 调用用户定义的 `material()` 函数获取材质输入
4. 对 Lit 材质评估所有光照（直接光 + 间接光 + 反射 + 雾）
5. 对 Unlit 材质直接使用材质发光色
6. 处理 alpha 混合模式（淡入/预乘/Screen）
7. 应用抖动（dithering）和暗角（vignette）后输出

## 依赖关系
- `surface_material.fs` - 材质接口
- `surface_lighting.fs` - 光照计算
- `inline_dithering.fs` / `inline_vignette.fs` - 后处理效果
