# surface_getters.fs / surface_getters.vs / surface_getters.cs

## 文件概述
表面着色器属性获取器（片段/顶点/计算着色器版本）。封装了对模型矩阵、法线矩阵、蒙皮变换、形态目标数据等渲染对象属性的访问。

## 核心函数（片段着色器）
- `getWorldFromModelMatrix()` / `getWorldFromModelNormalMatrix()` - 获取模型变换矩阵
- `getNormalizedViewportCoord()` - 获取归一化视口坐标
- 材质属性获取（UV、颜色、位置等）

## 核心函数（顶点着色器）
- `getPosition()` / `getMorphPosition()` - 获取顶点位置（含形态目标偏移）
- `getSkinning()` - 获取骨骼蒙皮变换
- `getTangents()` / `getUV0()` / `getUV1()` - 获取顶点属性

## 依赖关系
- `surface_types.glsl` - PerRenderableData、BoneData 结构体
- `surface_instancing.glsl` - 实例化支持
