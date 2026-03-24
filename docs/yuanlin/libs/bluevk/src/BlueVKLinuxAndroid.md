# BlueVKLinuxAndroid.cpp

## 文件概述

`BlueVKLinuxAndroid.cpp` 是 BlueVK 在 Linux 和 Android 平台上的 Vulkan 共享库加载实现。它根据不同的编译配置和环境变量，确定 Vulkan 库的文件路径并加载。

## 核心类/结构体/函数

### 命名空间 `bluevk`

- **`bool loadLibrary()`**: 确定并加载 Vulkan 共享库。返回 true 表示成功。路径确定按以下优先级：
  1. 如果定义了 `FILAMENT_VKLIBRARY_USE_ENV`，检查环境变量 `FILAMENT_VKLIBRARY_PATH`
  2. 如果定义了 `FILAMENT_VKLIBRARY_PATH` 编译宏，使用该自定义路径
  3. Android 默认使用 `libvulkan.so`
  4. Linux 默认使用 `libvulkan.so.1`
- **`void* getInstanceProcAddr()`**: 使用 `dlsym` 查找 `vkGetInstanceProcAddr` 符号。

### 静态变量
- **`static void* module`**: 存储动态库句柄。

## 关键实现逻辑

1. **安全考虑**: 环境变量 `FILAMENT_VKLIBRARY_PATH` 仅在 `FILAMENT_VKLIBRARY_USE_ENV` 编译开关启用时才会被读取。官方 GitHub 发布版本不启用此选项，防止通过环境变量注入恶意库。
2. **SwiftShader 支持**: `FILAMENT_VKLIBRARY_PATH` 编译宏常用于指向 SwiftShader 的 Vulkan 软件实现，便于无 GPU 环境下的测试。
3. 使用 `RTLD_NOW | RTLD_LOCAL` 标志加载，确保所有符号立即解析且不污染全局符号空间。
4. 加载失败时通过 `utils::slog.e` 输出错误日志。

## 依赖关系

- `<dlfcn.h>` -- 动态链接库接口
- `<stdio.h>` -- 标准 I/O
- `<stdlib.h>` -- `getenv`
- `<utils/Log.h>` -- Filament 日志系统
