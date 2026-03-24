# VulkanPlatformLinux.h

## 文件概述

定义了 `VulkanPlatformLinux` 类，是 Linux 平台上 Vulkan 后端的具体实现。支持 X11/XCB 窗口系统的 Vulkan Surface 创建。

## 核心类

### VulkanPlatformLinux (继承自 VulkanPlatform)

Linux 平台 Vulkan 实现。

## 主要 API

| 方法 | 说明 |
|------|------|
| `getSwapchainInstanceExtensions()` | 返回 Linux 平台所需的 Vulkan 实例扩展 |
| `createVkSurfaceKHR(nativeWindow, instance, flags)` | 从 X11/XCB 窗口创建 VkSurfaceKHR |

## 依赖关系

- `backend/platforms/VulkanPlatform.h` - 基类
