# PlatformEGLHeadless.h

## 文件概述

定义了 `PlatformEGLHeadless` 类，是仅支持无头（headless）SwapChain 的 EGL 平台实现。适用于不需要窗口显示的离屏渲染场景。

## 核心类

### PlatformEGLHeadless (继承自 PlatformEGL)

一个精简的 EGL 平台，仅支持无头渲染。

## 主要 API

| 方法 | 说明 |
|------|------|
| `createDriver(sharedContext, config)` | 创建 OpenGL 驱动 |
| `isOpenGL()` | 返回 true（使用桌面 OpenGL 而非 ES） |

## 依赖关系

- `backend/platforms/PlatformEGL.h` - 基类
