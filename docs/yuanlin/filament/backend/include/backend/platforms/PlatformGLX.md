# PlatformGLX.h

## 文件概述

定义了 `PlatformGLX` 类，是 Linux 平台上使用 GLX (OpenGL Extension to X Window System) 的 OpenGL 后端实现。支持额外共享上下文的创建。

## 核心类

### PlatformGLX (继承自 OpenGLPlatform)

Linux X11 + GLX 平台实现。

**私有成员**:
- `mGLXDisplay` - X11 Display 连接
- `mGLXContext` - 主 GLX 上下文
- `mGLXConfig` - 帧缓冲区配置
- `mAdditionalContexts` - 共享上下文映射（线程 ID -> GLXContext）

## 主要 API

| 方法 | 说明 |
|------|------|
| `createDriver(sharedGLContext, config)` | 创建 OpenGL 驱动 |
| `getOSVersion()` | 返回 0 |
| `terminate()` | 清理 GLX 资源 |
| `isExtraContextSupported()` / `createContext(shared)` / `releaseContext()` | 额外上下文管理 |
| SwapChain 系列方法 | 创建、销毁、绑定 SwapChain |
| `commit(swapChain)` | 呈现帧（glXSwapBuffers） |

## 依赖关系

- `backend/platforms/OpenGLPlatform.h` - 基类
- `backend/DriverEnums.h`
- `bluegl/BlueGL.h` - OpenGL 加载器
- `GL/glx.h` - GLX API
