# PlatformCocoaTouchGL.h

## 文件概述

定义了 `PlatformCocoaTouchGL` 类，是 iOS（CocoaTouch）平台上 OpenGL ES 后端的具体实现。支持 SwapChain 管理和 CVPixelBuffer 外部图像。在 iOS 上也被别名为 `ContextManager`。

## 核心类

### PlatformCocoaTouchGL (继承自 OpenGLPlatform)

iOS 上的 OpenGL ES 平台实现，使用 EAGLContext。

## 主要 API

| 方法 | 说明 |
|------|------|
| `createExternalImage(cvPixelBuffer)` | 从 CVPixelBuffer 创建外部图像 |
| `createDriver(sharedGLContext, config)` | 创建 OpenGL ES 驱动 |
| `getOSVersion()` | 返回 0 |
| `getDefaultFramebufferObject()` | 获取默认 FBO |
| `isExtraContextSupported()` / `createContext(shared)` | 额外上下文支持 |
| SwapChain 系列方法 | 创建、销毁、绑定 SwapChain |
| 外部纹理系列方法 | 创建、销毁、绑定外部纹理 |

## 类型别名

```cpp
using ContextManager = PlatformCocoaTouchGL;
```

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `backend/DriverEnums.h` - 枚举定义
