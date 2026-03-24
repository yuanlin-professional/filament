# BlueVKWindows.cpp

## 文件概述

`BlueVKWindows.cpp` 是 BlueVK 在 Windows 平台上的 Vulkan 共享库加载实现。它通过 Windows API `LoadLibraryA` 加载 Vulkan 运行时 DLL，并通过 `GetProcAddress` 获取函数入口。

## 核心类/结构体/函数

### 命名空间 `bluevk`

- **`bool loadLibrary()`**: 调用 `LoadLibraryA("vulkan-1.dll")` 加载 Vulkan 运行时。成功返回 true。
- **`void* getInstanceProcAddr()`**: 使用 `GetProcAddress` 从已加载的 DLL 中获取 `vkGetInstanceProcAddr` 的地址。

### 静态常量
- **`VKLIBRARY_PATH`**: 值为 `"vulkan-1.dll"`，Windows 平台 Vulkan 运行时的标准文件名。

### 静态变量
- **`static HMODULE library`**: 存储 `LoadLibraryA` 返回的模块句柄。

## 关键实现逻辑

Windows 上的 Vulkan 运行时由 GPU 驱动安装程序部署为 `vulkan-1.dll`，通常位于系统 PATH 中。实现非常简洁，直接使用 Windows API 的标准动态链接机制。

## 依赖关系

- `<windows.h>` -- Windows API（`LoadLibraryA`、`GetProcAddress`、`HMODULE`）
