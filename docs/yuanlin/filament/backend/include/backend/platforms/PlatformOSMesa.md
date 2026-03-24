# PlatformOSMesa.h

## 文件概述

定义了 `PlatformOSMesa` 类，使用 OSMesa（Off-Screen Mesa）实现离屏 OpenGL 渲染。OSMesa 是 Mesa 3D 的离屏上下文接口，支持软件光栅化。适用于无 GPU 的服务器端渲染或测试环境。

## 核心类

### PlatformOSMesa (继承自 OpenGLPlatform)

基于 OSMesa 的离屏 OpenGL 平台实现。

## 主要 API

| 方法 | 说明 |
|------|------|
| `createDriver(sharedGLContext, config)` | 创建 OpenGL 驱动 |
| `getOSVersion()` | 返回 0 |
| `terminate()` | 清理 OSMesa 资源 |
| `createSwapChain(nativewindow, flags)` | 创建 SwapChain |
| `createSwapChain(width, height, flags)` | 创建无头 SwapChain |
| `destroySwapChain(swapChain)` | 销毁 SwapChain |
| `makeCurrent(type, draw, read)` | 绑定上下文 |
| `commit(swapChain)` | 提交帧 |

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `backend/DriverEnums.h`
- `bluegl/BlueGL.h` - OpenGL 加载器
- `GL/osmesa.h` 或 `osmesa.h` - OSMesa API
