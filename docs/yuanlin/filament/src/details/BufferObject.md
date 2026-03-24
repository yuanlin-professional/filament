# BufferObject

## 文件概述

`FBufferObject` 是 `BufferObject` 公共 API 的私有实现类。它封装了一个后端缓冲区对象句柄，用于管理顶点数据或其他绑定类型的 GPU 缓冲区。提供创建、更新数据和销毁的基本生命周期管理。

## 核心类/结构体

### `FBufferObject`
- 继承自 `BufferObject`（公共 API 类）
- 持有 `backend::Handle<backend::HwBufferObject>` 句柄
- 通过 `FILAMENT_DOWNCAST(BufferObject)` 宏支持公共类到私有类的向下转型

### `BufferObject::BuilderDetails`
- Builder 模式的内部细节，包含 `mBindingType`（默认 VERTEX）和 `mByteCount`

## 关键实现逻辑

- **构造** -- 通过 Builder 参数调用 `driver.createBufferObject()` 创建后端缓冲区
- **setBuffer()** -- 调用 `driver.updateBufferObject()` 更新缓冲区数据，要求 `byteOffset` 为 4 字节对齐
- **terminate()** -- 销毁后端资源 `driver.destroyBufferObject()`

## 依赖关系

- `details/Engine.h` -- FEngine 引擎实例
- `backend/Handle.h` -- 后端资源句柄
- `filament/BufferObject.h` -- 公共 API 定义

## 与公共 API 的关系

直接对应 `filament::BufferObject`。用户通过 `BufferObject::Builder` 创建实例，最终调用 `FEngine::createBufferObject()` 构造 `FBufferObject`。
