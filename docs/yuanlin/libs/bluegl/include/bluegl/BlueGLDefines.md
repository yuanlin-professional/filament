# BlueGLDefines.h

## 文件概述

`BlueGLDefines.h` 是由 `bluegl-gen.py` 脚本自动生成的宏定义文件。它将标准 OpenGL 函数名（如 `glCullFace`）通过 `#define` 重映射为 BlueGL 前缀的函数名（如 `bluegl_glCullFace`），使得应用代码中使用的 OpenGL 调用被透明地重定向到 BlueGL 动态加载的函数指针。

## 核心类/结构体/函数

本文件不包含类或函数定义，仅包含大量宏定义，格式统一为：

```c
#define glXxx bluegl_glXxx
```

覆盖所有 OpenGL Core Profile 和常用扩展函数，例如：
- `glCullFace` -> `bluegl_glCullFace`
- `glTexImage2D` -> `bluegl_glTexImage2D`
- `glDrawBuffer` -> `bluegl_glDrawBuffer`

## 关键实现逻辑

通过预处理器宏替换实现零开销的函数名重定向。包含此头文件后，所有标准 GL 调用在编译期被替换为对应的 `bluegl_` 前缀包装函数，这些包装函数在 `BlueGLCoreWindows32Impl.cpp` 中定义，内部转发到动态加载的函数指针。

## 依赖关系

- 无外部头文件依赖
- 被 `<GL/glcorearb.h>` 和 `<GL/glext.h>` 间接使用
