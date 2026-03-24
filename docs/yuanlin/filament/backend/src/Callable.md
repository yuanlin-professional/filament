# Callable

## 文件概述

`Callable.cpp` 实现了 `PresentCallable` 类，用于封装帧展示回调。它是交换链提交流程的一部分，确保每个展示回调仅被调用一次。

## 核心类/结构体

### `PresentCallable`
- 头文件位于 `backend/PresentCallable.h`（公共头文件）。
- 封装一个函数指针 `PresentFn` 和用户数据指针 `mUser`。
- **构造函数**: 接受 `PresentFn` 和 `void* user`，断言函数指针非空。
- **operator()**: 调用展示函数并传入 `presentFrame` 布尔参数。调用后将函数指针置为 `nullptr`，防止重复调用。

## 关键实现逻辑

- 采用一次性调用模式（single-use callable）：通过在调用后将 `mPresentFn` 置空来实现。
- 二次调用时触发 `FILAMENT_CHECK_PRECONDITION` 前置条件检查，产生明确的错误信息。
- 这种设计防止了帧展示逻辑被意外多次执行导致的渲染错误。

## 依赖关系

- `backend/PresentCallable.h` - 类声明和 `PresentFn` 类型定义
- `utils::Panic` / `utils::debug` - 断言和前置条件检查
