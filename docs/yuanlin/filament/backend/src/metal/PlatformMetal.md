# PlatformMetal

## 文件概述

PlatformMetal 是 Metal 后端的平台抽象层实现，负责 Metal 设备和命令队列的创建与管理。作为 Filament 引擎与 Metal API 之间的桥梁，处理设备选择、命令队列初始化、drawable 失败策略等平台级配置。

**源文件**: `PlatformMetal.mm`（对应头文件位于 `backend/platforms/PlatformMetal.h`）

## 核心类/结构体

- **`PlatformMetal`** - 平台接口实现类
  - `initialize()` - 初始化 Metal 设备和命令队列
  - `createDriver()` - 通过 `MetalDriverFactory` 创建驱动实例
  - `createDevice()` / `createCommandQueue()` / `createAndEnqueueCommandBuffer()` - Metal 资源创建
  - `setDrawableFailureBehavior()` / `getDrawableFailureBehavior()` - drawable 获取失败策略
- **`PlatformMetalImpl`** - 私有实现（PIMPL 模式）
  - `mDevice` / `mCommandQueue` - Metal 设备和命令队列（mutex 保护）
  - `mDrawableFailureBehavior` - drawable 失败行为（原子变量，跨线程安全）

## 关键实现逻辑

- **设备选择**: 默认使用 `MTLCreateSystemDefaultDevice()`；在 macOS 上可通过环境变量 `FILAMENT_FORCE_INTEGRATED_GPU` 强制使用集成 GPU（低功耗设备）
- **PIMPL 模式**: 使用 `PlatformMetalImpl` 隐藏实现细节，避免头文件暴露 Objective-C 类型
- **线程安全**: 设备和命令队列的创建/访问通过 mutex 保护；drawable 失败策略使用 atomic（驱动线程读、客户端线程写）
- **Drawable 失败策略**: 两种模式 - `PANIC`（断言失败，默认）和 `ABORT_FRAME`（放弃当前帧，由 `DriverConfig::metalDisablePanicOnDrawableFailure` 控制）
- **惰性创建**: `createDeviceImpl` 和 `createCommandQueueImpl` 在首次调用时创建，后续调用返回缓存的对象
- **默认平台工厂**: `createDefaultMetalPlatform()` 全局函数返回默认 `PlatformMetal` 实例

## 依赖关系

- `backend/platforms/PlatformMetal.h`、`PlatformMetal-ObjC.h` - 平台接口声明
- `MetalDriverFactory.h` - 驱动创建
- `Foundation/Foundation.h` - NSProcessInfo（环境变量读取）
