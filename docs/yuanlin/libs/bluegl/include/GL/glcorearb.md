# glcorearb.h

## 文件概述

`glcorearb.h` 是 Khronos Group 官方提供的 OpenGL Core Profile 头文件，来源于 OpenGL Registry。它定义了 OpenGL 核心版本（1.0 至 4.6）的所有类型、常量和函数原型。在 BlueGL 中作为标准 GL 类型的来源被引用。

## 核心类/结构体/函数

### 平台调用约定宏
- **`APIENTRY`**: 函数调用约定，Windows 上为 `__stdcall`，其他平台为空。
- **`APIENTRYP`**: 函数指针调用约定（`APIENTRY *`）。
- **`GLAPI`**: 函数声明修饰符，默认为 `extern`。

### 类型定义
定义了所有 OpenGL 基础类型（`GLenum`、`GLint`、`GLuint`、`GLfloat` 等）以及各版本的函数指针类型（`PFN_xxx`）。

### 常量与枚举
定义了 OpenGL 所有版本的常量值，如 `GL_TRIANGLES`、`GL_TEXTURE_2D`、`GL_FRAMEBUFFER` 等。

## 关键实现逻辑

该文件使用条件编译 `#ifndef GL_VERSION_X_Y` 分隔各个 OpenGL 版本的定义，当定义 `GL_GLEXT_PROTOTYPES` 时会声明函数原型（BlueGL 在非 WGL 平台上启用此宏）。在 Windows 平台上会自动包含 `<windows.h>`。

## 依赖关系

- `<windows.h>` -- 仅 Windows 平台（提供 `APIENTRY` 等定义）
