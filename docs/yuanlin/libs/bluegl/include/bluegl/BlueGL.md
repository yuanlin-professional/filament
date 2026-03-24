# BlueGL.h / BlueGL.cpp

## 文件概述

BlueGL 是 Filament 渲染引擎的 OpenGL 函数动态加载库的核心模块。头文件 `BlueGL.h` 定义了公共 API 接口，`BlueGL.cpp` 实现了跨平台的 OpenGL 函数绑定与解绑逻辑。该文件由 `bluegl-gen.py` 脚本自动生成。

## 核心类/结构体/函数

### 命名空间 `bluegl`

- **`int bind()`**: 查找并绑定所有可用的 OpenGL Core 函数指针。内部维护引用计数器，每次调用引用计数递增。首次调用时通过平台特定的 `initBinder()` 初始化动态库，然后遍历 `g_gl_stubs` 数组加载所有函数地址。成功返回 0，失败返回 -1。
- **`void unbind()`**: 解绑所有 OpenGL Core 函数。引用计数递减，当计数归零时调用 `shutdownBinder()` 关闭动态库，并将所有函数指针置为 nullptr。

### 平台抽象接口（extern 声明）

- **`bool initBinder()`**: 平台相关，负责打开 OpenGL 动态库。
- **`void* loadFunction(const char* name)`**: 平台相关，按名称查找 GL 函数地址。
- **`void shutdownBinder()`**: 平台相关，关闭动态库句柄。

## 关键实现逻辑

1. 使用 `std::mutex` (`g_library_mutex`) 保护引用计数 `g_library_refcount`，确保多线程安全。
2. 首次 `bind()` 调用时遍历全局 `g_gl_stubs` 数组（共 2574 个函数），通过 `loadFunction` 获取地址，找不到的函数指向 `undefined()` 占位函数（Debug 模式会输出错误信息）。
3. 头文件在 Windows 平台上 `#undef` 了 `max`、`min`、`far`、`near`、`ERROR` 等宏，避免与 Windows SDK 头文件冲突。

## 依赖关系

- `<GL/glcorearb.h>` -- OpenGL Core Profile 类型与常量定义
- `<GL/glext.h>` -- OpenGL 扩展定义
- `<iostream>` -- 错误输出
- `<mutex>` -- 线程安全
- `"private_BlueGL.h"` -- 内部函数指针表与引用计数全局变量
