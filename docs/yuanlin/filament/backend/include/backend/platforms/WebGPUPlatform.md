# WebGPUPlatform.h

## 文件概述

定义了 `WebGPUPlatform` 抽象类，是 WebGPU 图形后端的平台基类。WebGPU 是一个现代的跨平台图形 API，该类处理与操作系统环境相关的部分（窗口大小、Surface 创建、适配器/设备请求等）。

## 核心类

### WebGPUPlatform (继承自 Platform)

**内部类型**:
- `Configuration` - 平台配置（可强制指定后端类型）

**受保护成员**:
- `mInstance` - `wgpu::Instance` WebGPU 实例

## 主要 API

| 方法 | 说明 |
|------|------|
| `getSurfaceExtent(nativeWindow)` | 纯虚函数，获取窗口/Surface 尺寸 |
| `createSurface(nativeWindow, flags)` | 纯虚函数，创建 wgpu::Surface |
| `requestAdapter(surface)` | 请求 GPU 适配器 |
| `requestDevice(adapter)` | 请求 GPU 设备 |
| `getInstance()` | 获取 wgpu::Instance 引用 |
| `getConfiguration()` | 获取平台配置 |
| `getAdapterOptions()` | 纯虚函数，获取适配器请求选项 |
| `createDriver(sharedContext, config)` | 创建 WebGPU 驱动 |

## 依赖关系

- `backend/Platform.h` - 基类
- `webgpu/webgpu_cpp.h` - WebGPU C++ API
