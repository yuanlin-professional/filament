# Driver

## 文件概述

`Driver.cpp` 和 `DriverBase.h` 共同定义了 Filament 后端驱动的基类体系。`DriverBase` 是所有具体图形 API 驱动（OpenGL、Vulkan、Metal 等）的公共基类，提供回调调度、资源销毁调度和调试命令支持等通用功能。

## 核心类/结构体

### 硬件资源句柄（`DriverBase.h` 中定义）
- **HwBase**: 所有硬件资源的空基类。
- **HwVertexBufferInfo** / **HwVertexBuffer** / **HwIndexBuffer** / **HwBufferObject**: 缓冲区资源句柄。
- **HwTexture**: 纹理资源句柄，包含宽、高、深度、采样数、格式、使用标志等完整元数据。
- **HwProgram**: 着色器程序句柄，携带名称字符串。
- **HwRenderTarget**: 渲染目标句柄，包含宽高。
- **HwFence** / **HwSync** / **HwSwapChain** / **HwStream** / **HwTimerQuery**: 其他资源句柄。
- **HwDescriptorSetLayout** / **HwDescriptorSet**: 描述符集布局和实例句柄。

### `DriverBase`
- **构造函数**: 启动 `ServiceThread` 用于在独立线程上执行用户回调。
- **scheduleCallback()**: 将回调分发到服务线程（有 `CallbackHandler` 时）或存入待清理队列。
- **purge()**: 在应用线程上执行无 handler 的回调。
- **scheduleDestroy()**: 调度 `BufferDescriptor` 的销毁回调。
- **scheduleRelease()**: 调度 `AcquiredImage` 的释放回调。
- **debugCommandBegin/End()**: 调试模式下在命令前后插入 systrace 标记。

### `Driver`
- **getElementTypeSize()**: 返回各 `ElementType` 的字节大小。
- **execute()**: 默认直接调用传入的函数，子类可重写以在特定上下文中执行（如 GL 上下文）。

## 关键实现逻辑

- `ServiceThread` 模式：在多线程环境下，用户回调通过服务线程异步执行，避免在渲染线程上处理。
- `CallbackData` 使用内联存储（`storage[8]`）避免小对象堆分配，仿函数直接 placement-new 到 storage 中。
- 服务线程使用条件变量等待回调队列非空，收集回调后释放锁再逐个调用，避免持锁时间过长。

## 依赖关系

- `private/backend/Driver.h` - Driver 虚接口
- `private/backend/Dispatcher.h` - 命令分发器
- `backend/Platform.h` - 平台抽象和配置
- `backend/CallbackHandler.h` - 回调处理器接口
- `utils::JobSystem` - 线程管理
