# SkinningBuffer.cpp

## 文件概述
SkinningBuffer.cpp 实现了蒙皮缓冲区的公共 API。SkinningBuffer 用于存储骨骼变换矩阵数据，支持 GPU 蒙皮动画。

## 核心类/结构体/函数
- **SkinningBuffer** - 蒙皮缓冲区公共接口
  - `setBones(Bone*, count, offset)` - 使用 Bone 结构（四元数+平移）设置骨骼变换
  - `setBones(mat4f*, count, offset)` - 使用 4x4 矩阵设置骨骼变换
  - `getBoneCount()` - 获取缓冲区可容纳的骨骼数量

## 关键实现逻辑
通过 `downcast` 委托给内部 `FSkinningBuffer`。支持两种骨骼数据格式：`Bone`（紧凑的四元数+平移表示）和 `mat4f`（完整的 4x4 变换矩阵）。

## 依赖关系
- `details/SkinningBuffer.h` - 内部实现
- `details/Engine.h` - 引擎

## 被引用关系
- `RenderableManager` - 通过 `setSkinningBuffer()` 绑定蒙皮缓冲区
- 用户层代码创建并填充骨骼动画数据
