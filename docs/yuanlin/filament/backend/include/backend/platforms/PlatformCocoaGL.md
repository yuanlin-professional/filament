# PlatformCocoaGL.h

## 文件概述

定义了 `PlatformCocoaGL` 类，是 macOS Cocoa 平台上 OpenGL 后端的具体实现。支持 SwapChain 管理、外部图像（CVPixelBuffer）和额外 GL 上下文创建。

## 核心类

### PlatformCocoaGL (继承自 OpenGLPlatform)

macOS 上的 OpenGL 平台实现，使用 NSOpenGLContext。

## 主要 API

| 方法 | 说明 |
|------|------|
| `createExternalImage(cvPixelBuffer)` | 从 CVPixelBuffer 创建外部图像 |
| `createDriver(sharedContext, config)` | 创建 OpenGL 驱动 |
| `getOSVersion()` | 返回 0 |
| `pumpEvents()` | 处理 macOS 事件循环 |
| `isExtraContextSupported()` / `createContext(shared)` | 额外上下文支持 |
| SwapChain 系列方法 | 创建、销毁、绑定 SwapChain |
| 外部纹理系列方法 | 创建、销毁、绑定外部纹理（CVPixelBuffer） |

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
