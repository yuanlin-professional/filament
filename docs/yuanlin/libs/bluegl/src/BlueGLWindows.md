# BlueGLWindows.cpp

## 文件概述

`BlueGLWindows.cpp` 是 BlueGL 在 Windows 平台上的动态库加载实现。它通过 `LoadLibraryA` 加载 Windows 系统的 `opengl32.dll`，并结合 `wglGetProcAddress` 和 `GetProcAddress` 两种方式查找 GL 函数地址。

## 核心类/结构体/函数

### 命名空间 `bluegl`

- **`bool initBinder()`**: 调用 `LoadLibraryA("opengl32.dll")` 加载 OpenGL 动态库。成功返回 true。
- **`void* loadFunction(const char* name)`**: 先尝试 `wglGetProcAddress` 查找函数地址；如果返回无效值（0、0x1、0x2、0x3 或 -1），则回退到 `GetProcAddress` 从 DLL 导出表中查找。
- **`void shutdownBinder()`**: 空实现，不释放 opengl32.dll 句柄。

### 静态变量
- **`static HMODULE module`**: 存储 `LoadLibraryA` 返回的模块句柄。

## 关键实现逻辑

Windows 的 OpenGL 函数查找需要两步策略：
1. `wglGetProcAddress` 用于查找 OpenGL 1.2+ 和扩展函数，但对 OpenGL 1.1 的基础函数返回 NULL。
2. `GetProcAddress` 用于从 `opengl32.dll` 直接获取 OpenGL 1.1 基础函数。
3. `wglGetProcAddress` 返回 0、1、2、3 或 -1 时均视为查找失败，需要回退。
4. `shutdownBinder()` 为空，因为 Windows 上 FreeLibrary 可能引起问题。

## 依赖关系

- `<windows.h>` -- Windows API（`LoadLibraryA`、`GetProcAddress`、`HMODULE`）
