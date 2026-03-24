# AndroidNdk

## 文件概述

`AndroidNdk.cpp` 实现了 Android NDK 函数的动态加载封装。通过 `dlsym` 在运行时加载 `libnativewindow.so` 中的 `AHardwareBuffer` 相关函数，以便在低版本 NDK 编译环境下安全调用高版本 API。

## 核心类/结构体

### `AndroidNdk`
- 头文件位于 `backend/platforms/AndroidNdk.h`（公共头文件）。
- **Ndk**: 静态结构体成员，存储动态加载的函数指针：
  - `AHardwareBuffer_acquire` - 增加硬件缓冲区引用计数
  - `AHardwareBuffer_release` - 释放硬件缓冲区引用计数
  - `AHardwareBuffer_describe` - 获取硬件缓冲区描述信息

## 关键实现逻辑

- 使用 `std::call_once` 确保 `dlopen`/`dlsym` 初始化仅执行一次。
- 条件编译宏 `FILAMENT_USE_DLSYM(26)` 控制是否在 API level 26 以下使用动态加载。
- `__builtin_available(android 26, *)` 运行时检查确保仅在支持的设备上调用。
- 模板函数 `loadSymbol` 简化 `dlsym` 调用并处理类型转换。

## 依赖关系

- `backend/platforms/AndroidNdk.h` - 公共头文件定义
- `<android/hardware_buffer.h>` - AHardwareBuffer 类型定义
- `<dlfcn.h>` - 动态库加载（dlopen, dlsym）
- `utils::compiler` - 编译器属性宏
