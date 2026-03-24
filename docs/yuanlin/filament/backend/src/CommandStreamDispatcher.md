# CommandStreamDispatcher

## 文件概述

`CommandStreamDispatcher.h` 实现了命令分发器的模板化代码生成机制。通过 `ConcreteDispatcher` 模板类，为每个具体的 Driver 实现（OpenGL、Vulkan、Metal、Noop 等）自动生成命令分发函数表。

## 核心类/结构体

### `ConcreteDispatcher<ConcreteDriver>`
- 模板类，以具体的 Driver 类型为参数。
- **make()**: 静态方法，生成并返回一个 `Dispatcher` 对象，其中每个 API 方法对应一个静态分发函数指针。
- 每个分发函数将 `Driver&` 向下转型为 `ConcreteDriver&`，然后调用对应的具体方法。

## 关键实现逻辑

- 通过 `DriverAPI.inc` 宏展开自动生成所有 Driver API 方法的分发函数。
- 三种宏分别处理不同类型的 API：
  - `DECL_DRIVER_API`: 普通异步命令
  - `DECL_DRIVER_API_RETURN`: 带返回值的命令（方法名后缀 `R`）
  - `DECL_DRIVER_API_SYNCHRONOUS`: 同步命令（不生成分发函数）
- `make()` 方法通过 `UTILS_NOINLINE` 标记防止内联，因为该函数体较大（包含所有 API 的函数指针赋值）。
- 可选的 `SYSTRACE()` 宏在 `DEBUG_LEVEL_SYSTRACE` 模式下为每个分发调用添加追踪。
- `Cmd::execute()` 负责从命令对象中提取参数并调用具体 Driver 方法。

## 依赖关系

- `private/backend/Driver.h` - Driver 基类和 Dispatcher 定义
- `private/backend/CommandStream.h` - 命令类型定义和 `COMMAND_TYPE` 宏
- `private/backend/DriverAPI.inc` - Driver API 宏定义文件（被包含三次）
- `private/utils/Tracing.h` - 性能追踪
