# BlueGLDarwin.cpp

## 文件概述

`BlueGLDarwin.cpp` 是 BlueGL 在 macOS (Darwin) 平台上的动态库加载实现。它负责打开 macOS 系统自带的 OpenGL framework，并通过 `dlsym` 查找 GL 函数地址。

## 核心类/结构体/函数

### 命名空间 `bluegl`

- **`bool initBinder()`**: 使用 `dlopen` 打开 `/System/Library/Frameworks/OpenGL.framework/OpenGL`，标志为 `RTLD_GLOBAL | RTLD_NOW`。成功返回 true。
- **`void* loadFunction(const char* name)`**: 使用 `dlsym` 在已打开的 OpenGL framework 中查找指定函数名称对应的符号地址。
- **`void shutdownBinder()`**: 调用 `dlclose` 关闭动态库句柄，并将 `library` 指针重置为 nullptr。

### 静态变量
- **`static void* library`**: 存储 `dlopen` 返回的动态库句柄。

## 关键实现逻辑

macOS 上的 OpenGL 实现位于系统 framework 中，路径固定为 `/System/Library/Frameworks/OpenGL.framework/OpenGL`。直接通过 `dlsym` 即可查找所有 Core Profile 函数，无需像 Linux 那样使用 `glXGetProcAddress`。

## 依赖关系

- `<dlfcn.h>` -- POSIX 动态链接库接口（`dlopen`、`dlsym`、`dlclose`）
