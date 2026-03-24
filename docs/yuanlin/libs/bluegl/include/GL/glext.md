# glext.h

## 文件概述

`glext.h` 是 Khronos Group 官方提供的 OpenGL 扩展头文件，来源于 OpenGL Registry。它定义了所有已注册的 OpenGL 扩展的常量、类型和函数指针。文件体积较大（约 780KB），包含数百个扩展定义。

## 核心类/结构体/函数

### 扩展函数指针类型
为每个扩展定义了 `PFN_xxx` 类型的函数指针，例如：
- `PFNGLBINDFRAGDATALOCATIONINDEXEDPROC`
- `PFNGLDRAWARRAYSINDIRECTPROC`

### 扩展常量
为各扩展定义了枚举常量，如 `GL_ARB_texture_storage`、`GL_ARB_compute_shader` 等。

### 扩展版本宏
`GL_GLEXT_VERSION` 标识当前扩展规范版本号。

## 关键实现逻辑

使用 `#ifndef GL_XXX_extension_name` 的条件编译模式，每个扩展独立保护，避免重复定义。当定义了 `GL_GLEXT_PROTOTYPES` 时会声明函数原型供静态链接使用，否则仅提供函数指针类型供动态加载。

## 依赖关系

- `<GL/glcorearb.h>` -- 提供基础 GL 类型定义
- `<windows.h>` -- 仅 Windows 平台
