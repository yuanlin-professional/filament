# PlatformWGL.h

## 文件概述

定义了 `PlatformWGL` 类，是 Windows 平台上使用 WGL (Windows OpenGL) 的 OpenGL 后端实现。支持额外共享上下文的创建。

## 核心类

### PlatformWGL (继承自 OpenGLPlatform)

Windows WGL 平台实现。

**受保护成员**:
- `mContext` - 主 WGL 上下文（HGLRC）
- `mHWnd` - 窗口句柄
- `mWhdc` - 设备上下文
- `mPfd` - 像素格式描述符
- `mAdditionalContexts` - 共享上下文列表

## 主要 API

| 方法 | 说明 |
|------|------|
| `createDriver(sharedGLContext, config)` | 创建 OpenGL 驱动 |
| `getOSVersion()` | 返回 0 |
| `terminate()` | 清理 WGL 资源 |
| `isExtraContextSupported()` / `createContext(shared)` | 额外上下文支持 |
| SwapChain 系列方法 | 创建、销毁、绑定 SwapChain |
| `commit(swapChain)` | 呈现帧（SwapBuffers） |

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `backend/DriverEnums.h`
- `windows.h` - Windows API
