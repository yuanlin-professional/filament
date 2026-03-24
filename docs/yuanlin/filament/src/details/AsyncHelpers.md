# AsyncHelpers.h

## 文件概述

`AsyncHelpers.h` 是一个纯头文件，提供异步回调辅助工具类。它定义了两个模板类，用于将用户提供的回调函数适配到 Filament 内部回调系统，主要服务于异步资源创建流程（如异步创建 IndexBuffer、VertexBuffer 等）。

## 核心类/结构体

### `CallbackAdapter<T>`
- 简单的回调适配器，将用户回调 `std::function<void(T*, void*)>` 桥接为 Filament 回调系统所需的 `CallbackHandler::Callback*` 形式
- 通过静态工厂方法 `make()` 创建实例，回调完成后自动 `delete` 自身

### `CountdownCallbackHandler<T>`
- 继承自 `backend::CallbackHandler`
- 跟踪多个异步操作，在所有操作完成后才执行用户回调
- 内部使用 `std::atomic_uint32_t` 计数器实现倒计时机制
- 使用 `std::memory_order_acq_rel` 确保跨线程内存可见性

## 关键实现逻辑

- `increaseCountdown()` -- 每发起一个异步调用时由用户手动递增
- `countdownCallback()` -- 每个异步操作完成时递减计数器，归零时触发最终回调
- `invokeUserCallback()` -- 通过用户指定的 CallbackHandler 调度执行真正的用户回调
- 回调链路：ServiceThread 调用 `post()` -> `countdownCallback()` -> 调度 `invokeUserCallback()`

## 依赖关系

- `private/backend/Driver.h` -- 后端驱动接口
- `backend/CallbackHandler.h` -- 回调处理器基类

## 与公共 API 的关系

不直接对应公共 API，作为内部工具类被 `FIndexBuffer`、`FVertexBuffer` 等异步创建流程使用。支撑 `IndexBuffer::Builder::async()`、`VertexBuffer::Builder::async()` 等公共异步 API。
