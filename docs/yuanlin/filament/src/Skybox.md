# Skybox.cpp

## 文件概述
Skybox.cpp 实现了天空盒的公共 API。Skybox 用于渲染场景背景，支持立方体贴图纹理或纯色背景。

## 核心类/结构体/函数
- **Skybox** - 天空盒公共接口
  - `setLayerMask()` / `getLayerMask()` - 图层掩码控制（决定天空盒在哪些 View 层中可见）
  - `getIntensity()` - 获取天空盒光照强度
  - `setColor()` - 设置纯色背景（float4，RGBA）
  - `getTexture()` - 获取关联的立方体贴图纹理

## 关键实现逻辑
通过 `downcast` 委托给内部 `FSkybox`。天空盒可以是纹理立方体贴图或纯色。

## 依赖关系
- `details/Skybox.h` - 内部实现
- `details/Texture.h` - 纹理

## 被引用关系
- `Scene` - 通过 `setSkybox()` 设置场景天空盒
