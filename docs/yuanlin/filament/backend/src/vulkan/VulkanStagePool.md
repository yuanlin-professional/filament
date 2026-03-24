# VulkanStagePool (VulkanStagePool.h / VulkanStagePool.cpp)

## 文件概述

CPU-GPU 暂存缓冲区（Staging Buffer）池管理器，用于纹理上传和缓冲区更新。采用分段分配策略，在大块连续缓冲区内按需划分子段，减少 VkBuffer 创建次数。

## 核心类/结构体

### `VulkanStage` 类
一块连续的暂存缓冲区（CPU 可见 + GPU 可读），可被细分为多个 Segment：
- 持有 VMA 分配的 VkBuffer 和内存映射指针
- 支持子段的分配和回收

### `VulkanStage::Segment` 类
暂存缓冲区的一个子段，继承自 Resource：
- 持有父 Stage 引用、容量、偏移量和回收回调
- 析构时通过 OnRecycle 回调将占用的偏移量归还父 Stage

### `VulkanStageImage::Resource` 类
用于图像暂存的专用资源，管理暂存图像的 VkImage 和关联内存。

### `VulkanStagePool` 类
暂存缓冲区池管理器：
- `acquireStage(numBytes)` - 获取满足大小要求的暂存段
- `acquireImage(...)` - 获取暂存图像（用于 blit 格式转换等）
- `gc()` / `terminate()` - 垃圾回收和终止

## 关键实现逻辑

- **分段分配**: 大 Stage 内部按偏移量管理多个 Segment，避免为每次上传创建新 VkBuffer
- **空闲回收**: Segment 使用完后通过回调归还偏移空间，gc 时回收长时间未使用的 Stage
- **对齐处理**: 分配时考虑 texel block size 对齐要求

## 依赖关系

- `VulkanMemory.h` - VMA 分配器
- `backend/DriverEnums.h` - 后端枚举
- `vulkan/memory/Resource.h` / `ResourceManager.h` / `ResourcePointer.h` - 资源管理
