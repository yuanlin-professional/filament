# TypedUniformBuffer

## 文件概述

`TypedUniformBuffer.h` 在 `TypedBuffer` 基础上增加了 GPU 端 UBO（Uniform Buffer Object）句柄的管理，将 CPU 端数据和 GPU 端缓冲对象关联起来。

## 核心类/结构体

- **`TypedUniformBuffer<T, N>`**: 模板类，封装 `TypedBuffer<T,N>` 和后端 `BufferObjectHandle`。
  - `mTypedBuffer`: 内嵌的 TypedBuffer，管理 CPU 端数据
  - `mUboHandle`: GPU 端缓冲对象句柄
  - `init(driver)`: 创建大小为 `sizeof(T)*N` 的 DYNAMIC UNIFORM 缓冲对象
  - `terminate(driver)`: 销毁缓冲对象
  - 代理 `TypedBuffer` 的所有接口：`edit()`、`itemAt()`、`isDirty()` 等

## 关键实现逻辑

- 生命周期分为两步：构造时可不初始化 GPU 资源，通过 `init()` 延迟创建。`terminate()` 必须在析构前调用。
- 析构函数断言 `mUboHandle` 已被释放，防止资源泄漏。
- 缓冲对象使用 `BufferUsage::DYNAMIC` 模式，适合每帧更新的 uniform 数据。

## 依赖关系

- `ds/TypedBuffer.h` - CPU 端类型化缓冲区
- `backend/Handle.h` - `BufferObjectHandle`
- `backend/DriverEnums.h` - `BufferObjectBinding::UNIFORM`、`BufferUsage::DYNAMIC`
- `backend/DriverApiForward.h` - 驱动 API 前向声明
