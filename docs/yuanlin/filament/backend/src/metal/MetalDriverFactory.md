# MetalDriverFactory

## 文件概述

MetalDriverFactory 是 Metal 后端驱动的工厂类，提供统一的创建接口。实际实现位于 `MetalDriver.mm` 中，工厂方法简单地委托给 `MetalDriver::create()`。

**源文件**: `MetalDriverFactory.h`（仅头文件，实现在 `MetalDriver.mm`）

## 核心类/结构体

- **`MetalDriverFactory`** - 工厂类
  - `static Driver* create(PlatformMetal*, const Platform::DriverConfig&)` - 创建 Metal 驱动实例

## 关键实现逻辑

- 该工厂方法是 Metal 后端的入口点，由 `PlatformMetal::createDriver()` 调用
- 内部直接调用 `MetalDriver::create()` 完成实际创建

## 依赖关系

- `backend/Platform.h` - Platform::DriverConfig 配置
- `MetalDriver.h` - 实际驱动实现
