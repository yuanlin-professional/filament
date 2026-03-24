# BufferDescriptor.h

## 文件概述

定义了 `BufferDescriptor` 类，用于描述 CPU 内存缓冲区，通常用于从 CPU 向 GPU 传输数据。该类拥有其引用的内存缓冲区的所有权，因此不可复制但可移动。当 `BufferDescriptor` 被销毁时，会通过回调释放内存所有权。

## 核心类

### BufferDescriptor

CPU 内存缓冲区描述符，管理缓冲区的生命周期和释放回调。

**公开成员**:
- `void* buffer` - CPU 缓冲区虚拟地址
- `size_t size` - 缓冲区大小（字节）

**类型定义**:
- `Callback` - 缓冲区释放回调：`void(*)(void* buffer, size_t size, void* user)`

## 主要 API

| 方法 | 说明 |
|------|------|
| `BufferDescriptor(buffer, size, callback, user)` | 构造函数，引用 CPU 缓冲区并设置释放回调 |
| `BufferDescriptor(buffer, size, handler, callback, user)` | 带自定义回调处理器的构造函数 |
| `make<T, &T::method>(buffer, size, data, handler)` | 静态工厂方法，使用成员函数指针作为回调 |
| `make<T>(buffer, size, functor, handler)` | 静态工厂方法，使用函子作为回调（会进行堆分配） |
| `setCallback(callback, user)` | 设置或替换释放回调 |
| `hasCallback()` | 检查是否设置了回调 |
| `getCallback()` | 获取当前回调函数 |
| `getHandler()` | 获取回调处理器 |
| `getUser()` | 获取用户数据指针 |

## 依赖关系

- `utils/compiler.h` - 编译器工具宏（如 `UTILS_PUBLIC`）
