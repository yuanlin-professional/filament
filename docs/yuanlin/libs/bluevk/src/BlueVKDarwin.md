# BlueVKDarwin.cpp

## 文件概述

`BlueVKDarwin.cpp` 是 BlueVK 在 macOS (Darwin) 平台上的 Vulkan 共享库加载实现。它负责通过 `dlopen` 打开 Vulkan 动态库（通常由 MoltenVK 或 LunarG SDK 提供），并获取 `vkGetInstanceProcAddr` 入口地址。

## 核心类/结构体/函数

### 命名空间 `bluevk`

- **`bool loadLibrary()`**: 加载 Vulkan 动态库。如果定义了 `FILAMENT_VKLIBRARY_PATH` 编译宏则使用自定义路径，否则默认加载 `libvulkan.1.dylib`。使用 `RTLD_NOW | RTLD_LOCAL` 标志。加载失败时通过 `FILAMENT_CHECK_POSTCONDITION` 抛出错误信息，提示安装 LunarG SDK。
- **`void* getInstanceProcAddr()`**: 使用 `dlsym` 从已加载的库中查找 `vkGetInstanceProcAddr` 符号。

### 静态变量
- **`static void* module`**: 存储 `dlopen` 返回的动态库句柄。

## 关键实现逻辑

macOS 上 Vulkan 支持通过 MoltenVK（Metal 上的 Vulkan 实现层）提供。默认库路径为 `libvulkan.1.dylib`，这是 LunarG Vulkan SDK 安装后的标准路径。可通过 `FILAMENT_VKLIBRARY_PATH` 编译宏自定义路径（例如指向 SwiftShader 的 Vulkan 实现）。

## 依赖关系

- `<utils/Panic.h>` -- Filament 断言宏 `FILAMENT_CHECK_POSTCONDITION`
- `<dlfcn.h>` -- POSIX 动态链接库接口
