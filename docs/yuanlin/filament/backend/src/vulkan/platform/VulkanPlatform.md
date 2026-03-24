# VulkanPlatform (VulkanPlatform.cpp)

## 文件概述

Vulkan 平台层的核心实现文件，负责 Vulkan 实例创建、物理设备选择、逻辑设备创建、队列初始化以及交换链管理。这是 Filament Vulkan 后端的主入口点，`createDriver()` 方法完成整个 Vulkan 上下文初始化流程。

## 核心类/结构体

### `VulkanPlatformPrivate` 结构体
VulkanPlatform 的私有实现（PIMPL 模式），存储 Vulkan 核心句柄：
- VkInstance、VkPhysicalDevice、VkDevice
- 图形队列族索引、队列索引、VkQueue（普通和受保护版本）
- VulkanContext 上下文数据
- mSharedContext / mForceXCBSwapchain 标志

### `VulkanPlatform` 类（实现部分）
平台抽象层，核心方法：
- `createDriver()` - 主入口，初始化整个 Vulkan 环境并创建 VulkanDriver
- `createInstance()` - 创建 VkInstance，配置验证层和扩展
- `queryAndSetDeviceFeatures()` - 查询并设置设备特性（动态渲染、受保护内存等）
- `createLogicalDeviceAndQueues()` - 创建逻辑设备和命令队列
- `createSwapChain()` - 创建表面或无头交换链

## 关键实现逻辑

- **物理设备选择**: `selectPhysicalDevice()` 按设备类型优先级排序（离散GPU > 集成GPU > CPU），支持按名称或索引指定
- **扩展管理**: 分别枚举实例扩展和设备扩展，通过 `pruneExtensions()` 处理互斥扩展（如 debugUtils 与 debugMarkers）
- **特性链接**: 使用 `chainStruct()` 辅助函数将多个 Vulkan 特性结构体链接到 pNext 链
- **受保护内存**: 查询 `VkPhysicalDeviceProtectedMemoryFeatures`，创建独立的受保护队列
- **共享上下文**: 支持外部提供的 `VulkanSharedContext`，跳过实例/设备创建步骤
- **UMA 检测**: `hasUnifiedMemoryArchitecture()` 检查所有内存堆是否都有 DEVICE_LOCAL 标志

## 依赖关系

- `backend/platforms/VulkanPlatform.h` - 平台抽象接口声明
- `vulkan/VulkanContext.h` - Vulkan 上下文数据结构
- `vulkan/VulkanDriver.h` - 驱动创建
- `vulkan/VulkanConstants.h` - 常量和调试宏
- `vulkan/platform/VulkanPlatformSwapChainImpl.h` - 交换链实现
- `vulkan/utils/Helper.h` - Vulkan 枚举辅助函数
- `bluevk/BlueVK.h` - Vulkan 函数加载器
