# BlueGLLinuxEGL.cpp

## 文件概述

`BlueGLLinuxEGL.cpp` 是 BlueGL 在 Linux 平台（使用 EGL 窗口系统）上的动态库加载实现。与 `BlueGLLinux.cpp` 的 GLX 实现不同，此文件通过 EGL 接口加载 OpenGL 函数，适用于不依赖 X11/GLX 的环境（如 Wayland 或无头渲染）。

## 核心类/结构体/函数

### 结构体 `Driver`
```cpp
struct Driver {
    void* (*eglGetProcAddress)(const char*);
    void* library;
};
```
持有 EGL 库句柄和函数查询入口。

### 命名空间 `bluegl`

- **`bool initBinder()`**: 使用 `dlopen` 加载 `libEGL.so.1`（`RTLD_GLOBAL | RTLD_NOW`），然后获取 `eglGetProcAddress` 函数指针。
- **`void* loadFunction(const char* name)`**: 通过 `eglGetProcAddress` 查找指定的 GL 函数地址。
- **`void shutdownBinder()`**: 关闭库并清零 `g_driver`。

## 关键实现逻辑

EGL 是 Khronos 定义的跨平台窗口系统接口，`eglGetProcAddress` 可查询 OpenGL ES 和 OpenGL 的函数地址。此实现使用与 GLX 版本相同的策略：先获取查询函数，再通过它加载所有 GL 函数。

## 依赖关系

- `<dlfcn.h>` -- 动态链接库接口
- `<string.h>` -- memset
