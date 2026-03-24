# VirtualMachineEnv.h

## 文件概述

定义了 `VirtualMachineEnv` 类，用于管理 Android JNI（Java Native Interface）环境。封装了 JavaVM 的线程绑定和 JNIEnv 获取逻辑，确保后端线程能安全地与 Java 虚拟机交互。

## 核心类

### VirtualMachineEnv

JNI 环境管理器，属于 `filament` 命名空间（而非 `filament::backend`）。

## 主要 API

| 方法 | 说明 |
|------|------|
| `JNI_OnLoad(vm)` | 静态方法，必须在 `get()` 之前从已附加 JVM 的线程调用 |
| `get()` | 静态方法，获取单例实例（必须从后端线程调用） |
| `getThreadEnvironment()` | 静态方法，从任意已有 JNIEnv 的线程获取 JNIEnv |
| `getEnvironment()` | 获取当前线程的 JNIEnv（后端线程专用，带快速路径优化） |
| `handleException(env)` | 静态方法，处理 JNI 异常 |

## 设计说明

- `getEnvironment()` 使用内联快速路径：如果 `mJniEnv` 已缓存则直接返回，否则走慢速路径（`getEnvironmentSlow()`）进行 JVM 线程附加
- 使用互斥锁保护 JavaVM 的静态访问

## 依赖关系

- `utils/compiler.h` - 编译器工具
- `utils/Mutex.h` - 互斥锁
- `jni.h` - JNI 头文件
