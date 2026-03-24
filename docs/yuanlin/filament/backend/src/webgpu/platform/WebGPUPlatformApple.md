# WebGPUPlatformApple

## 文件概述

Apple 平台（macOS 和 iOS）的 WebGPU 后端实现（Objective-C++ 文件），处理 Metal 层的 surface 创建和尺寸获取。

## 核心实现

- **适配器选项**: 请求 Metal 和 Vulkan 后端（Vulkan 用于软件光栅化支持），高/低功耗偏好
- **Surface 创建**: 使用 `wgpu::SurfaceSourceMetalLayer` 从 `CAMetalLayer` 创建
- **尺寸获取**: 通过 `CAMetalLayer.drawableSize` 获取

## 依赖关系

- `backend/platforms/WebGPUPlatformApple.h` - 平台子类声明
- `QuartzCore/CAMetalLayer.h` - Apple Metal 层
