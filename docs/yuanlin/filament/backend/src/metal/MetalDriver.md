# MetalDriver

## 文件概述

MetalDriver 是 Filament Metal 后端的核心驱动实现，继承自 `DriverBase`，实现了 Filament 后端驱动 API 的全部方法。负责资源创建/销毁、渲染通道管理、绘制调用分发、纹理/缓冲区操作等所有 GPU 交互。

**源文件**: `MetalDriver.h`, `MetalDriver.mm`

## 核心类/结构体

- **`MetalDriver`** - 后端驱动主类（`final`），核心功能：
  - 通过 `#include "private/backend/DriverAPI.inc"` 宏展开声明所有驱动 API 方法
  - `HandleAllocatorMTL` 管理句柄分配与类型安全的 handle_cast
  - 模板方法 `alloc_handle`、`construct_handle`、`destruct_handle` 实现句柄的分配/构造/析构
  - 延迟任务系统：`DeferredTask` 在指定命令缓冲区完成后执行
  - Tick 操作：`runAtNextTick` / `executeTickOps` 在每帧驱动线程执行
- **`MetalDriverFactory`** - 工厂类，`create()` 静态方法创建 `MetalDriver` 实例

## 关键实现逻辑

- **驱动 API 实现**: 通过宏展开 `DriverAPI.inc` 生成所有方法声明，在 .mm 文件中逐一实现
- **执行环境**: `execute()` 方法用 `@autoreleasepool` 包裹每次调用，确保 Objective-C 对象及时释放
- **句柄系统**: 8MB 的句柄分配区，支持不同大小的资源对象（从 16 字节的 TimerQuery 到 552 字节的 VertexBufferInfo）
- **延迟任务**: 命令缓冲区完成后在驱动线程执行清理任务，通过比较 `commandBufferId` 和 `latestCompletedCommandBufferId` 判断完成状态
- **着色器语言**: 返回 MSL 和 METAL_LIBRARY 两种着色器语言支持
- **立体渲染**: 支持配置 `StereoscopicType` 和 `AsynchronousMode`

## 依赖关系

- `MetalBlitter.h`、`MetalBufferPool.h`、`MetalContext.h`、`MetalEnums.h`、`MetalHandles.h`、`MetalState.h`、`MetalTimerQuery.h`、`MetalUtils.h` - Metal 后端内部模块
- `private/backend/Driver.h`、`DriverBase.h` - 驱动基类
- `private/backend/HandleAllocator.h` - 句柄分配器
- `backend/platforms/PlatformMetal.h` - 平台接口
- `Metal/Metal.h`、`QuartzCore/QuartzCore.h`、`CoreVideo/` - Apple 框架
