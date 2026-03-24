# PlatformWebGL.h

## 文件概述

定义了 `PlatformWebGL` 类，是 WebGL 平台（Emscripten/WebAssembly）的 OpenGL ES 后端实现。用于在浏览器环境中运行 Filament。

## 核心类

### PlatformWebGL (继承自 OpenGLPlatform)

WebGL 平台实现，功能相对精简。

## 主要 API

| 方法 | 说明 |
|------|------|
| `createDriver(sharedGLContext, config)` | 创建 WebGL 驱动 |
| `getOSVersion()` | 获取 OS 版本 |
| `terminate()` | 清理资源 |
| `createSwapChain(nativeWindow, flags)` | 创建 SwapChain |
| `createSwapChain(width, height, flags)` | 创建无头 SwapChain |
| `destroySwapChain(swapChain)` | 销毁 SwapChain |
| `makeCurrent(type, draw, read)` | 绑定上下文 |
| `commit(swapChain)` | 提交帧 |

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `backend/DriverEnums.h`
