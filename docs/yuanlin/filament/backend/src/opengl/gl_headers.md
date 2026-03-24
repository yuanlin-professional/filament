# gl_headers.h / gl_headers.cpp

## 文件概述

gl_headers 是 OpenGL 后端的头文件配置中枢，负责根据不同平台（Android、iOS、macOS、WebGL、Linux）引入正确的 OpenGL/OpenGL ES 头文件，并提供 GLES 扩展入口点的动态加载。

## 核心类/结构体

### 命名空间 `glext`
- 声明并定义了所有需要通过 `eglGetProcAddress` 动态加载的 GLES 扩展函数指针。
- `importGLESExtensionsEntryPoints()`: 线程安全地初始化所有扩展入口点，使用 `std::call_once` 保证只执行一次。

### 宏定义
- `BACKEND_OPENGL_VERSION_GLES` / `BACKEND_OPENGL_VERSION_GL`: 标识 GLES 或桌面 GL。
- `BACKEND_OPENGL_LEVEL_GLES31/30/20`: 标识支持的 GLES 功能级别。
- `FILAMENT_SILENCE_NOT_SUPPORTED_BY_ES2`: 用于编译时排除 ES2 不支持的代码路径。

## 关键实现逻辑

- 头文件通过条件编译选择正确的 GL 头文件：Android/Emscripten 使用 GLES3 头文件，iOS 使用 OpenGLES 框架，桌面平台使用 BlueGL。
- cpp 文件在 `FILAMENT_IMPORT_ENTRY_POINTS` 定义时，通过 EGL 动态加载所有扩展函数指针。
- 将 GLES 扩展常量（如 `GL_TIME_ELAPSED_EXT`）映射为统一的名称以简化代码。

## 依赖关系

- `GLES3/gl31.h` 或 `GLES2/gl2ext.h` (Android/Emscripten)
- `OpenGLES/ES3/gl.h` (iOS)
- `bluegl/BlueGL.h` (桌面平台)
- `NullGLES.h` - 调试用空操作存根
- `EGL/egl.h` (cpp 中用于 `eglGetProcAddress`)
