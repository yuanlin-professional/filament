# surface_main.vs

## 文件概述
表面材质顶点着色器主入口。处理实例化索引、立体渲染视图选择、蒙皮/形态目标变形、以及用户自定义顶点操作。

## 核心函数
- `main()` - 顶点着色器入口

## 关键实现逻辑
1. 处理实例化索引（Metal/Vulkan 使用 `gl_InstanceIndex`，OpenGL 使用 `gl_InstanceID`）
2. PowerVR 驱动 workaround：对 `gl_InstanceID` 进行额外整数运算
3. 立体实例化渲染：计算逻辑实例索引 = 实例索引 % 眼睛数量
4. 支持优化深度顶点着色器路径（`USE_OPTIMIZED_DEPTH_VERTEX_SHADER`）

## 依赖关系
- `surface_getters.vs` - 顶点属性获取
- `surface_instancing.glsl` - 实例化支持
