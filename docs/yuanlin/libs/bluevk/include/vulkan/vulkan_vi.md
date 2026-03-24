# vulkan_vi.h

## 文件概述

`vulkan_vi.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Nintendo Vi 平台扩展头文件，定义了在 Nintendo Switch (NN/Vi) 平台上创建 Vulkan Surface 的扩展。

## 核心类/结构体/函数

### VK_NN_vi_surface
- **`VkViSurfaceCreateInfoNN`**: 包含 Vi 窗口指针
- **`vkCreateViSurfaceNN`**: 从 Vi 窗口创建 Vulkan Surface

## 关键实现逻辑

通过 `VK_USE_PLATFORM_VI_NN` 宏条件编译。Vi 是 Nintendo Switch 的窗口系统接口。Filament 一般不在此平台上运行。

## 依赖关系

- `vulkan_core.h` -- Vulkan 核心类型
