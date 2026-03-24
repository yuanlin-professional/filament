# OpenGLPlatform.h

## 文件概述

定义了 `OpenGLPlatform` 抽象类，是所有 OpenGL/OpenGL ES 后端平台实现的基类。提供了 OpenGL 上下文管理、SwapChain、Fence、Stream、外部纹理等功能的接口。

**重要约束**：所有方法不允许修改 GL 状态，调用后必须恢复原始状态。

## 核心类

### OpenGLPlatform (继承自 Platform)

**内部类型**:
- `ExternalTexture` - 外部纹理（`target` + GL `id`）
- `ContextType` - 上下文类型（NONE/UNPROTECTED/PROTECTED）

## 主要 API

### 上下文与 SwapChain 管理
| 方法 | 说明 |
|------|------|
| `terminate()` | 销毁 OpenGL 上下文 |
| `createSwapChain(nativeWindow, flags)` | 从原生窗口创建 SwapChain |
| `createSwapChain(width, height, flags)` | 创建无头 SwapChain |
| `destroySwapChain(swapChain)` | 销毁 SwapChain |
| `makeCurrent(type, draw, read)` | 绑定上下文和 SwapChain |
| `commit(swapChain)` | 呈现帧（如 eglSwapBuffers） |
| `getDefaultFramebufferObject()` | 获取默认 FBO（默认返回 0） |

### Fence / Sync
| 方法 | 说明 |
|------|------|
| `canCreateFence()` | 是否支持 Fence |
| `createFence()` / `destroyFence()` / `waitFence()` | Fence 操作 |
| `createSync()` / `destroySync()` | Sync 操作 |

### 外部纹理与流
| 方法 | 说明 |
|------|------|
| `createExternalImageTexture()` / `destroyExternalImageTexture()` | 创建/销毁外部纹理 |
| `setExternalImage(image, texture)` | 将外部图像绑定到纹理 |
| `createStream(nativeStream)` | 创建外部纹理流 |
| `attach(stream, tname)` / `detach(stream)` | 绑定/解绑流与纹理 |
| `updateTexImage(stream, timestamp)` | 更新流内容到纹理 |

### 其他
| 方法 | 说明 |
|------|------|
| `isExtraContextSupported()` / `createContext(shared)` | 额外 GL 上下文支持 |
| `isSRGBSwapChainSupported()` / `isProtectedContextSupported()` | 功能查询 |

## 依赖关系

- `backend/AcquiredImage.h` / `backend/DriverEnums.h` / `backend/Platform.h`
- `utils/compiler.h` / `utils/Invocable.h` / `utils/CString.h`
- `math/mat3.h`
