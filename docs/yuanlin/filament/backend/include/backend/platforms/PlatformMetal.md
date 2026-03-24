# PlatformMetal.h

## 文件概述

定义了 `PlatformMetal` 类，是 Apple Metal 图形后端的平台实现。支持自定义 Metal 设备和命令队列的创建，以及 Drawable 获取失败时的行为配置。

## 核心类

### PlatformMetal (继承自 Platform, final)

**内部枚举**:
- `DrawableFailureBehavior` - Drawable 获取失败行为：
  - `PANIC` - 终止应用并报错（默认）
  - `ABORT_FRAME` - 中止当前帧，下一帧重试

## 主要 API

| 方法 | 说明 |
|------|------|
| `initialize()` | 可选初始化，提前获取 Metal 设备和命令队列 |
| `createDriver(sharedContext, config)` | 创建 Metal 驱动 |
| `createDevice(outDevice)` | 虚方法，选择首选 Metal 设备 |
| `createCommandQueue(device, outQueue)` | 虚方法，创建命令队列 |
| `createAndEnqueueCommandBuffer(outBuffer)` | 获取排队的命令缓冲区（主线程调用） |
| `setDrawableFailureBehavior(behavior)` | 设置 Drawable 失败行为 |
| `getDrawableFailureBehavior()` | 获取当前失败行为 |

## 使用说明

- `initialize()` 可多次安全调用，首次成功后后续调用无效
- `initialize()` 和 `createAndEnqueueCommandBuffer()` 必须在主线程调用
- `createDevice()` 和 `createCommandQueue()` 从后端线程调用

## 依赖关系

- `backend/Platform.h` - 基类
- `backend/DriverEnums.h` - 枚举定义
- 前向声明 `MetalDevice` / `MetalCommandQueue` / `MetalCommandBuffer`（见 `PlatformMetal-ObjC.h`）
