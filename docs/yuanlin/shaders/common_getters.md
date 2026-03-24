# common_getters.glsl

## 文件概述
通用 uniform 访问器函数集。提供对视图矩阵、投影矩阵、相机参数等 frame uniform 的封装访问，以及立体渲染的眼睛索引获取。

## 核心函数
- `getEyeIndex()` - 获取当前渲染眼睛索引（支持实例化立体和多视图立体）
- `getViewFromWorldMatrix()` - 获取世界到视图空间变换矩阵
- `getWorldFromViewMatrix()` - 获取视图到世界空间变换矩阵
- `getClipFromViewMatrix()` / `getViewFromClipMatrix()` - 投影/反投影矩阵
- `getWorldPosition()` - 获取相机世界坐标

## 关键实现逻辑
- 立体渲染通过 `gl_ViewIndex`（Vulkan）或 `gl_ViewID_OVR`（OpenGL 多视图）获取眼睛索引
- 所有矩阵通过 `frameUniforms` 结构体访问

## 依赖关系
- `frameUniforms` - 引擎注入的每帧 uniform 数据
