# vulkan.h

## 文件概述

`vulkan.h` 是 Khronos Group 官方提供的 Vulkan 主头文件，作为统一入口包含 Vulkan Core 定义和所有平台特定的窗口系统集成（WSI）头文件。BlueVK 通过此文件获取所有 Vulkan 类型、常量和 `PFN_` 函数指针类型定义。

## 核心类/结构体/函数

本文件不直接定义类或函数，而是作为头文件聚合器，根据平台宏条件包含各平台 WSI 头文件。

### 平台条件包含
- `VK_USE_PLATFORM_ANDROID_KHR` -> `vulkan_android.h`
- `VK_USE_PLATFORM_FUCHSIA` -> `vulkan_fuchsia.h`
- `VK_USE_PLATFORM_IOS_MVK` -> `vulkan_ios.h`
- `VK_USE_PLATFORM_MACOS_MVK` -> `vulkan_macos.h`
- `VK_USE_PLATFORM_METAL_EXT` -> `vulkan_metal.h`
- `VK_USE_PLATFORM_WAYLAND_KHR` -> `vulkan_wayland.h`
- `VK_USE_PLATFORM_WIN32_KHR` -> `vulkan_win32.h`
- `VK_USE_PLATFORM_XCB_KHR` -> `vulkan_xcb.h`
- `VK_USE_PLATFORM_XLIB_KHR` -> `vulkan_xlib.h`
- `VK_ENABLE_BETA_EXTENSIONS` -> `vulkan_beta.h`

## 关键实现逻辑

通过 `#ifdef` 条件编译，根据目标平台选择性包含对应的 WSI 扩展头文件。BlueVK 在 `BlueVK.h` 中预先定义了 `VK_NO_PROTOTYPES` 和对应的平台宏，确保只引入类型定义而不引入函数原型声明。

## 依赖关系

- `"vk_platform.h"` -- 平台调用约定定义
- `"vulkan_core.h"` -- Vulkan 核心 API 类型和常量
- 各平台 WSI 头文件（条件包含）
