# VulkanDriverFactory (VulkanDriverFactory.h)

## 文件概述

Vulkan 驱动工厂类，提供创建 Vulkan 后端驱动实例的静态工厂方法。作为外部代码创建 Vulkan 驱动的简化入口点。

## 核心类/结构体

### `VulkanDriverFactory` 类
工厂类，仅包含一个静态方法：
- `create(platform, ppEnabledExtensions, enabledExtensionCount, driverConfig)` - 创建并返回 Vulkan Driver 实例

## 关键实现逻辑

- **简化入口**: 将 VulkanPlatform 和扩展配置封装为单一创建接口
- **平台抽象**: 通过 VulkanPlatform 指针间接访问平台特定功能

## 依赖关系

- `backend/Platform.h` - Platform::DriverConfig 定义
