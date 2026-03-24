# BlueGLCoreWindows32Impl.cpp

## 文件概述

`BlueGLCoreWindows32Impl.cpp` 是由 `bluegl-gen.py` 脚本自动生成的文件，包含所有 OpenGL Core 函数的 BlueGL 包装实现。每个 `bluegl_glXxx` 函数是对应 `glXxx` 的转发包装，通过动态加载的函数指针实现间接调用。尽管文件名含有 "Windows32"，但实际上它提供的包装函数在所有平台上都被使用。

## 核心类/结构体/函数

文件为每个 OpenGL 函数生成了统一模式的包装函数，总计约 2574 个，格式如下：

```cpp
extern void* __blue_glCore_glXxx;
void APIENTRY bluegl_glXxx(params...) {
    typedef void (APIENTRYP PFN_glXxx)(params...);
    return ((PFN_glXxx)__blue_glCore_glXxx)(params...);
}
```

包括但不限于：`bluegl_glCullFace`、`bluegl_glFrontFace`、`bluegl_glHint`、`bluegl_glLineWidth` 等。

## 关键实现逻辑

1. 每个包装函数声明一个局部函数指针类型 `PFN_glXxx`。
2. 将全局 `void*` 指针 `__blue_glCore_glXxx`（在 `private_BlueGL.h` 中定义）强制转换为该函数指针类型。
3. 通过转换后的函数指针调用实际的 OpenGL 驱动函数。
4. 全部函数以 `extern "C"` 链接方式导出，避免 C++ 名称修饰。

## 依赖关系

- `<bluegl/BlueGL.h>` -- BlueGL 头文件（提供 GL 类型定义）
- `__blue_glCore_glXxx` 全局变量 -- 由 `private_BlueGL.h` 定义，在 `BlueGL.cpp` 的 `bind()` 中被赋值
