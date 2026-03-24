# PlatformWGL.cpp

## 文件概述

Windows 平台的 WGL（Windows GL）上下文管理实现，通过 Win32 API 和 WGL 扩展创建和管理 OpenGL 上下文。

## 核心类/结构体

### `PlatformWGL`（通过实现文件推断）
- 使用 Win32 的 `CreateWindowEx` 创建不可见窗口作为 GL 上下文的载体。
- 使用 `wglCreateContextAttribsARB` 创建 OpenGL 核心配置文件上下文。
- 通过 `SetPixelFormat` 和 `ChoosePixelFormat` 选择像素格式。

### 辅助函数
- `reportWindowsError()`: 使用 `FormatMessage` 格式化 Windows 错误信息。

## 关键实现逻辑

- 创建过程需要两步：先创建一个临时的旧式上下文以获取 WGL 扩展函数指针，再使用扩展创建现代 OpenGL 核心上下文。
- 使用 Pbuffer 作为离屏渲染表面（通过 `wglCreatePbufferARB`）。
- 处理 `FILAMENT_PLATFORM_WGL` 宏以避免与 BlueGL 的 OpenGL API 重定义冲突。

## 依赖关系

- `backend/platforms/PlatformWGL.h`
- `Wingdi.h`, `Windows.h` - Win32 API
- `GL/gl.h`, `GL/glext.h`, `GL/wglext.h` - WGL 扩展
- `gl_headers.h` - Filament GL 头文件
