# WebGPUDriver

## 文件概述

`WebGPUDriver` 是 Filament 后端驱动 API (`DriverAPI.inc`) 的 WebGPU 具体实现。它是 WebGPU 后端的核心入口，管理所有 WebGPU 资源（设备、队列、管线缓存、纹理、缓冲区等）的创建和销毁。

## 核心类/结构体

- **`WebGPUDriver`** (继承 `DriverBase`) - WebGPU 后端驱动
  - `mPlatform` - 平台抽象层引用
  - `mDevice` / `mAdapter` - WebGPU 设备和适配器
  - `mQueueManager` - 命令队列管理器
  - `mStagePool` - 暂存缓冲区池
  - `mPipelineCache` / `mPipelineLayoutCache` - 管线和布局缓存
  - `mRenderPassMipmapGenerator` / `mSpdComputePassMipmapGenerator` - 两种 mipmap 生成器
  - `mMsaaTextureResolver` - MSAA 纹理解析器
  - `mBlitter` - 纹理 blit 操作器
  - `mHandleAllocator` - 句柄分配器（8MB 竞技场）

## 关键实现逻辑

1. **DriverAPI 宏展开**: 通过 `#include "private/backend/DriverAPI.inc"` 宏展开声明所有驱动 API 方法
2. **句柄管理**: 使用模板化的 `allocHandle`/`constructHandle`/`destructHandle` 进行类型安全的句柄分配
3. **描述符集绑定**: `mCurrentDescriptorSets` 数组跟踪当前绑定的 BindGroup 及动态偏移

## 依赖关系

- 几乎依赖 webgpu/ 下的所有其他文件
- `DriverBase.h` / `private/backend/Driver.h` - 驱动抽象基类
- `private/backend/HandleAllocator.h` - 句柄内存分配
- `backend/platforms/WebGPUPlatform.h` - 平台抽象
