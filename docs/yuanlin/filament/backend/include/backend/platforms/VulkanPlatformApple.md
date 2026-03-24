# VulkanPlatformApple.h

## 文件概述

定义了 `VulkanPlatformApple` 类，是 Apple 平台（macOS/iOS）上 Vulkan 后端的具体实现。通过 MoltenVK 在 Metal 之上提供 Vulkan 支持。

## 核心类

### VulkanPlatformApple (继承自 VulkanPlatform)

Apple 平台 Vulkan 实现，仅需实现交换链相关的平台特定方法。

## 主要 API

| 方法 | 说明 |
|------|------|
| `getSwapchainInstanceExtensions()` | 返回 Apple 平台所需的 Vulkan 实例扩展 |
| `createVkSurfaceKHR(nativeWindow, instance, flags)` | 从原生窗口创建 VkSurfaceKHR |

## 依赖关系

- `backend/platforms/VulkanPlatform.h` - 基类
