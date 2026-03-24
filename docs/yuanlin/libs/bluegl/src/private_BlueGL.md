# private_BlueGL.h

## 文件概述

`private_BlueGL.h` 是由 `bluegl-gen.py` 脚本自动生成的内部头文件，定义了 BlueGL 所有 OpenGL 函数的全局函数指针变量、函数指针映射表结构以及库引用计数等内部状态。此文件不对外暴露，仅供 BlueGL 内部实现使用。

## 核心类/结构体/函数

### 全局函数指针变量
以 `extern "C"` 链接声明，每个 GL 函数对应一个 `void*` 指针：
```cpp
void* __blue_glCore_glCullFace;
void* __blue_glCore_glFrontFace;
void* __blue_glCore_glHint;
// ... 共约 2574 个
```

### 映射表结构（推断）
包含 `g_gl_stubs` 数组，每个元素存储函数名称字符串和对应 `void**` 指针的映射关系，供 `bind()` 遍历加载。

### 全局状态变量
- **`g_library_mutex`**: `std::mutex`，保护引用计数的线程安全。
- **`g_library_refcount`**: `uint32_t`，记录 `bind()` / `unbind()` 调用次数。
- **`blueCoreNumFunctions`**: 函数总数常量（2574）。

## 关键实现逻辑

函数指针以 `__blue_glCore_` 为前缀命名，使用 `extern "C"` 链接避免 C++ 名称修饰。这些指针在 `BlueGL.cpp` 的 `bind()` 中被赋值，在 `BlueGLCoreWindows32Impl.cpp` 的包装函数中被间接调用。

## 依赖关系

- `<mutex>` -- std::mutex（推断，用于 g_library_mutex）
- `<cstdint>` -- uint32_t 等类型
