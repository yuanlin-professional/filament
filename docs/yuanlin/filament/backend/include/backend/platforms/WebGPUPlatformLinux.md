# WebGPUPlatformLinux.h

## 文件概述

定义了 `WebGPUPlatformLinux` 类，是 Linux 平台上 WebGPU 后端的具体实现。

## 核心类

### WebGPUPlatformLinux (继承自 WebGPUPlatform)

Linux 平台的 WebGPU 实现。

## 主要 API

| 方法 | 说明 |
|------|------|
| `getSurfaceExtent(nativeWindow)` | 获取 Linux 窗口尺寸 |
| `createSurface(nativeWindow, flags)` | 从原生窗口创建 wgpu::Surface |
| `getAdapterOptions()` | 返回 Linux 平台的适配器请求选项 |

## 依赖关系

- `backend/platforms/WebGPUPlatform.h` - 基类
