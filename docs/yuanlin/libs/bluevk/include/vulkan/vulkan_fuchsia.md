# vulkan_fuchsia.h

## 文件概述

`vulkan_fuchsia.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Fuchsia OS 平台扩展头文件，定义了 Google Fuchsia 操作系统上的 Vulkan 扩展，包括 ImagePipe Surface、Buffer Collection 和 Zircon 句柄互操作。

## 核心类/结构体/函数

### VK_FUCHSIA_imagepipe_surface
- **`vkCreateImagePipeSurfaceFUCHSIA`**: 从 Fuchsia ImagePipe 创建 Vulkan Surface

### VK_FUCHSIA_external_memory / VK_FUCHSIA_external_semaphore
- Zircon VMO 与 Vulkan 内存/信号量的互操作

### VK_FUCHSIA_buffer_collection
- Fuchsia Sysmem Buffer Collection 集成

## 关键实现逻辑

通过 `VK_USE_PLATFORM_FUCHSIA` 宏条件编译。Fuchsia 使用 Zircon 微内核，其 IPC 机制基于 Zircon handle。Filament 一般不在 Fuchsia 上运行。

## 依赖关系

- `<zircon/types.h>` -- Zircon 内核类型
- `vulkan_core.h` -- Vulkan 核心类型
