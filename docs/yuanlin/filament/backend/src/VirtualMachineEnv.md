# VirtualMachineEnv

## 文件概述

`VirtualMachineEnv.cpp` 实现了 Android JNI 环境的管理类。提供 Java 虚拟机的线程本地 `JNIEnv` 获取、线程自动附加/分离，以及 JNI 异常处理等功能。仅在 Android 平台使用。

## 核心类/结构体

### `VirtualMachineEnv`
- 头文件位于 `private/backend/VirtualMachineEnv.h`。
- **JNI_OnLoad()**: 静态方法，在 JNI 库加载时调用。验证 JNI 版本兼容性并保存 `JavaVM*`。使用互斥锁确保仅初始化一次。
- **get()**: 返回线程本地的 `VirtualMachineEnv` 实例（`thread_local`），确保每个线程有独立的 JNI 环境。
- **getThreadEnvironment()**: 获取当前线程的 `JNIEnv*`，要求线程已附加到 JVM。
- **getEnvironmentSlow()**: 首次获取时将当前线程附加到 JVM（`AttachCurrentThread`）。在 Android 上设置线程名称。

## 关键实现逻辑

- 使用 `thread_local` 存储每线程的 `VirtualMachineEnv` 实例，析构时自动调用 `DetachCurrentThread`。
- `sVirtualMachine` 静态变量通过 `Mutex` 保护，`Mutex` 本身不受静态初始化顺序问题影响（初始状态为单个 int 0）。
- JNI 线程附加采用延迟初始化策略：只在首次需要 `JNIEnv` 时才附加线程，避免不必要的开销。
- **handleException()**: 检查并清除 JNI 异常，通过 `ExceptionDescribe()` 输出异常信息后调用 `ExceptionClear()`。

## 依赖关系

- `private/backend/VirtualMachineEnv.h` - 类声明
- `<jni.h>` - JNI 接口
- `<pthread.h>` - Android 线程名称获取
- `utils::Mutex` / `utils::Panic` / `utils::debug` - 同步和错误处理
