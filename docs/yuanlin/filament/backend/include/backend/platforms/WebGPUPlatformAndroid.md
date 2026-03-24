# WebGPUPlatformAndroid.h

## 文件概述

定义了 `WebGPUPlatformAndroid` 类，是 Android 平台上 WebGPU 后端的具体实现。

## 核心类

### WebGPUPlatformAndroid (继承自 WebGPUPlatform)

Android 平台的 WebGPU 实现。

## 主要 API

| 方法 | 说明 |
|------|------|
| `getSurfaceExtent(nativeWindow)` | 获取 Android 原生窗口尺寸 |
| `createSurface(nativeWindow, flags)` | 从 ANativeWindow 创建 wgpu::Surface |
| `getAdapterOptions()` | 返回 Android 平台的适配器请求选项 |

## 依赖关系

- `backend/platforms/WebGPUPlatform.h` - 基类
