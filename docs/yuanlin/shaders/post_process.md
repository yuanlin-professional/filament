# post_process_main.fs / post_process_main.vs / post_process_inputs.fs / post_process_inputs.vs / post_process_getters.vs

## 文件概述
后处理着色器框架。提供后处理通道（如色调映射、bloom、FXAA 等）的顶点和片段着色器模板，包括全屏三角形生成和纹理坐标传递。

## 核心文件
- `post_process_main.vs` - 后处理顶点着色器入口（生成全屏三角形）
- `post_process_main.fs` - 后处理片段着色器入口（调用用户 `postProcess()` 函数）
- `post_process_inputs.vs/fs` - 后处理输入结构体定义
- `post_process_getters.vs` - 后处理顶点属性获取器

## 依赖关系
- `common_math.glsl` - 数学工具
- `common_getters.glsl` - 矩阵获取
