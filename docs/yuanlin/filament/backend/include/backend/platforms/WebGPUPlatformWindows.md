# WebGPUPlatformWindows.h

## 文件概述

定义了 `WebGPUPlatformWindows` 类，是 Windows 平台上 WebGPU 后端的具体实现。

## 核心类

### WebGPUPlatformWindows (继承自 WebGPUPlatform)

Windows 平台的 WebGPU 实现。

## 主要 API

| 方法 | 说明 |
|------|------|
| `getSurfaceExtent(nativeWindow)` | 获取 Windows 窗口尺寸 |
| `createSurface(nativeWindow, flags)` | 从 HWND 创建 wgpu::Surface |
| `getAdapterOptions()` | 返回 Windows 平台的适配器请求选项 |

## 依赖关系

- `backend/platforms/WebGPUPlatform.h` - 基类
