# vulkan_directfb.h

## 文件概述

`vulkan_directfb.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 DirectFB 平台扩展头文件，定义了在 DirectFB 窗口系统上创建 Vulkan Surface 的扩展。DirectFB 是一个轻量级嵌入式 Linux 图形框架。

## 核心类/结构体/函数

### VK_EXT_directfb_surface
- **`VkDirectFBSurfaceCreateInfoEXT`**: 包含 `IDirectFB` 和 `IDirectFBSurface` 指针
- **`vkCreateDirectFBSurfaceEXT`**: 从 DirectFB surface 创建 Vulkan Surface
- **`vkGetPhysicalDeviceDirectFBPresentationSupportEXT`**: 查询 DirectFB 呈现支持

## 关键实现逻辑

通过 `VK_USE_PLATFORM_DIRECTFB_EXT` 宏条件编译。DirectFB 在嵌入式 Linux 系统中使用，Filament 一般不使用此平台。

## 依赖关系

- `<directfb.h>` -- DirectFB 库类型
- `vulkan_core.h` -- Vulkan 核心类型
