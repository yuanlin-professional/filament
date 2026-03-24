# CallbackHandler.h

## 文件概述

定义了 `CallbackHandler` 抽象接口，用于调度后端回调到用户期望的线程。所有接受回调参数的 API 都同时接受一个 `CallbackHandler*`，由其负责将回调调度到适当的线程上执行。

## 核心类

### CallbackHandler

通用回调调度接口。设计目的是让回调与平台/操作系统的消息系统互操作。

**类型定义**:
- `Callback` - 回调函数类型：`void(*)(void* user)`

**生命周期说明**:
- Filament 不管理 `CallbackHandler` 的生命周期，也不获取其所有权
- `CallbackHandler` 实例必须在所有待处理回调被调度完成之前保持有效
- 当 `CallbackHandler*` 为 `nullptr` 时，使用默认处理器（在 Filament 主线程上调度）

## 主要 API

| 方法 | 说明 |
|------|------|
| `post(void* user, Callback callback)` | 纯虚函数，将回调调度到适当线程执行。必须线程安全。 |

## 设计要点

- `post()` 从服务线程调用（绝不会是主线程）
- `post()` 的职责是将回调调度到用户期望的线程（通常是应用主线程）
- 关闭 Filament 时，必须确保所有访问 Filament 状态的待处理回调已被调度

## 依赖关系

无外部头文件依赖。
