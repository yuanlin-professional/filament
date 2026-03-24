# VulkanReadPixels (VulkanReadPixels.h / VulkanReadPixels.cpp)

## 文件概述

GPU 像素回读实现，将渲染目标的图像数据从 GPU 内存传输到 CPU 内存。使用后台线程处理数据转换工作，避免阻塞渲染线程。

## 核心类/结构体

### `VulkanReadPixels` 类
像素回读管理器，协调 GPU -> CPU 的数据传输流程。

### `TaskHandler` 内部类
后台任务执行器：
- `post(workload, oncomplete)` - 提交工作负载和完成回调
- `drain()` - 阻塞等待所有任务完成
- `shutdown()` - 关闭处理器（未执行的任务仍会调用 oncomplete 回调）

## 关键实现逻辑

- **异步回读**: 使用 VkBuffer 作为暂存缓冲区，通过 vkCmdCopyImageToBuffer 将图像数据拷贝到 CPU 可见内存
- **后台处理**: 数据格式转换（如通道重排、翻转等）在后台线程执行
- **围栏同步**: 通过 VulkanCmdFence 等待拷贝命令完成后再读取暂存缓冲区
- **回调清理**: TaskHandler 保证即使在 shutdown 时也会调用 oncomplete 回调

## 依赖关系

- `vulkan/memory/ResourcePointer.h` - 资源引用
- `private/backend/Driver.h` - 回读回调类型
- `bluevk/BlueVK.h` - Vulkan 类型
- `math/vec4.h` - 向量运算
