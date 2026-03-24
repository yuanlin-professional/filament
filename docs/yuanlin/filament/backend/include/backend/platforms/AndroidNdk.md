# AndroidNdk.h

## 文件概述

定义了 `AndroidNdk` 类，封装了 Android NDK 中 `AHardwareBuffer` 相关的 API。在编译目标 API 级别低于 26 时，通过 `dlsym` 动态加载这些函数；否则直接调用系统 API。

## 核心类

### AndroidNdk

Android NDK 硬件缓冲区 API 的封装层。

**静态方法**:
- `AHardwareBuffer_acquire(buffer)` - 增加硬件缓冲区引用计数
- `AHardwareBuffer_release(buffer)` - 减少硬件缓冲区引用计数
- `AHardwareBuffer_describe(buffer, desc)` - 获取硬件缓冲区描述信息

## 设计说明

通过条件编译宏 `FILAMENT_USE_DLSYM(api)` 决定是否使用 `dlsym` 动态加载：
- `__ANDROID_API__ < 26` 时：运行时动态查找函数符号
- `__ANDROID_API__ >= 26` 时：直接调用 NDK 提供的函数

## 依赖关系

- `android/native_window.h` - Android 原生窗口
- `android/hardware_buffer.h` - AHardwareBuffer API
