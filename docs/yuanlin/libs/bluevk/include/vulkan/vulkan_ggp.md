# vulkan_ggp.h

## 文件概述

`vulkan_ggp.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Google Games Platform (GGP/Stadia) 扩展头文件，定义了在 GGP 平台上创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_GGP_stream_descriptor_surface
- **`VkStreamDescriptorSurfaceCreateInfoGGP`**: 包含 GGP 流描述符
- **`vkCreateStreamDescriptorSurfaceGGP`**: 从 GGP 流描述符创建 Vulkan Surface

## 关键实现逻辑

通过 `VK_USE_PLATFORM_GGP` 宏条件编译。GGP (Google Games Platform) 是 Google Stadia 云游戏平台使用的内部平台。该平台已于 2023 年停止运营，此头文件仅为历史兼容性保留。

## 依赖关系

- `<ggp_c/vulkan_types.h>` -- GGP 类型定义
- `vulkan_core.h` -- Vulkan 核心类型
