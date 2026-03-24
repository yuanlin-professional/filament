# TypedUniformBuffer.h

## 文件概述
TypedBuffer（文件名为 TypedUniformBuffer.h）是一个类型安全的 Uniform 缓冲区模板，用于存储特定类型的 Uniform 数据结构。与通用的 UniformBuffer 不同，它在编译期确定数据类型和大小。

## 核心类/结构体/函数
- **TypedBuffer\<T, N\>** - 类型化缓冲区模板（T = 数据类型，N = 数组大小，默认 1）
  - `edit()` - 获取第一个元素的可变引用（标记为脏）
  - `itemAt(i)` - 获取第 i 个元素的可变引用
  - `getSize()` - 返回缓冲区总字节数（sizeof(T) * N）
  - `isDirty()` / `clean()` - 脏标记管理
  - `toBufferDescriptor()` - 拷贝数据到驱动分配的缓冲区并标记为干净

## 关键实现逻辑
- 内部使用固定大小数组 `T mBuffer[N]` 存储数据
- 任何写入操作（`edit()` / `itemAt()`）自动设置脏标记
- `toBufferDescriptor()` 支持偏移和大小参数，允许部分上传
- 使用 `memcpy` 进行数据拷贝，性能高效

## 依赖关系
- `private/backend/DriverApi.h` - 驱动 API（allocate 内存）
- `backend/BufferDescriptor.h` - 缓冲区描述符

## 被引用关系
- `PostProcessManager` - `TypedUniformBuffer<PerViewUib>` 存储每视图 Uniform
- 各种描述符集类 - 存储结构化 Uniform 数据
