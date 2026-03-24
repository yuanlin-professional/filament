# BlueGLLinux.cpp

## 文件概述

`BlueGLLinux.cpp` 是 BlueGL 在 Linux 平台（使用 GLX 窗口系统）上的动态库加载实现。它负责打开 `libGL.so.1` 共享库，并通过 `glXGetProcAddressARB` 查找 GL 函数地址。

## 核心类/结构体/函数

### 结构体 `Driver`
```cpp
struct Driver {
    void* (*glXGetProcAddress)(const GLubyte*);
    void* library;
};
```
持有动态库句柄和 GLX 函数查找入口。

### 命名空间 `bluegl`

- **`bool initBinder()`**: 使用 `dlopen` 加载 `libGL.so.1`（`RTLD_GLOBAL | RTLD_NOW`），然后从中获取 `glXGetProcAddressARB` 函数指针。
- **`void* loadFunction(const char* name)`**: 通过 `glXGetProcAddressARB` 查找指定的 GL 函数地址。
- **`void shutdownBinder()`**: 调用 `dlclose` 关闭库，`memset` 清零 `g_driver` 结构体。

## 关键实现逻辑

Linux GLX 环境下，某些 GL 扩展函数无法直接通过 `dlsym` 获取，必须使用 `glXGetProcAddressARB` 进行查询。因此先从 `libGL.so.1` 中获取该查询函数本身的地址，再用它来加载所有其他 GL 函数。

## 依赖关系

- `<GL/gl.h>` -- GLubyte 等基础类型
- `<dlfcn.h>` -- 动态链接库接口
- `<string.h>` -- memset
