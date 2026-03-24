# PlatformNoop

## 文件概述

`PlatformNoop.h` / `PlatformNoop.cpp` 实现了空操作后端的平台抽象层。作为 `Platform` 的最简实现，仅负责创建 `NoopDriver` 实例，不执行任何平台特定的初始化操作。

## 核心类/结构体

### `PlatformNoop`
- 继承自 `Platform`，标记为 `final`。
- **getOSVersion()**: 返回 0（无实际操作系统版本）。
- **getDeviceInfo()**: 返回空字符串。
- **createDriver()**: 核心方法，忽略 `sharedContext` 和 `driverConfig` 参数，直接调用 `NoopDriver::create()` 创建并返回 NoopDriver 实例。

## 关键实现逻辑

- 这是最简单的 Platform 实现，仅起到工厂方法的作用。
- `PlatformFactory::create()` 在 `Backend::NOOP` 模式下创建此类实例。
- 所有平台相关的功能（窗口管理、GL 上下文等）均不需要，因为 NoopDriver 不执行实际渲染。

## 依赖关系

- `backend/Platform.h` - Platform 基类
- `backend/DriverEnums.h` - 后端枚举定义
- `noop/NoopDriver.h` - NoopDriver 工厂方法
