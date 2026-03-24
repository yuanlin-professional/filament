# BlueGLOSMesa.cpp

## 文件概述

`BlueGLOSMesa.cpp` 是 BlueGL 对 OSMesa（Off-Screen Mesa）软件渲染后端的支持实现。OSMesa 允许在没有 GPU 或显示设备的环境下进行 OpenGL 渲染，常用于 CI/CD 测试和服务器端渲染。

## 核心类/结构体/函数

### 类型别名
```cpp
using ProcAddressFunc = void*(*)(char const* funcName);
```

### 结构体 `Driver`
```cpp
struct Driver {
    ProcAddressFunc OSMesaGetProcAddress;
    void* library;
};
```

### 命名空间 `bluegl`

- **`bool initBinder()`**: 尝试 `dlopen` 加载 OSMesa 库，Linux 上尝试 `libOSMesa.so` 和 `libosmesa.so`，macOS 上尝试 `libOSMesa.dylib`。如果动态加载成功，从库中获取 `OSMesaGetProcAddress`；Linux 上还支持静态链接和动态库内嵌的回退策略。
- **`void* loadFunction(const char* name)`**: 通过 `OSMesaGetProcAddress` 查找函数地址。
- **`void shutdownBinder()`**: 关闭库（如果有）并清零 `g_driver`。

## 关键实现逻辑

1. 使用 `__attribute__((weak))` 弱符号声明 `OSMesaGetProcAddress`，确保即使未链接 OSMesa 也不会导致编译失败。
2. 提供三级回退策略（Linux 平台）：
   - 首选：从动态加载的 OSMesa 库获取
   - 回退 1：通过 `dlsym(RTLD_LOCAL, ...)` 从当前进程空间查找（Filament 作为动态库时）
   - 回退 2：使用弱符号直接调用（静态链接 OSMesa 时）
3. macOS 上仅支持动态加载方式。

## 依赖关系

- `<dlfcn.h>` -- 动态链接库接口
- `<string.h>` -- memset
- `<osmesa.h>` 或 `<GL/osmesa.h>` -- OSMesa 类型定义（平台相关路径）
