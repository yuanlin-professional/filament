# vulkan_metal.h

## 文件概述

`vulkan_metal.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Metal 平台扩展头文件。定义了 macOS/iOS 上通过 Metal 后端实现 Vulkan 所需的扩展，包括 Metal Surface 创建和 Metal 对象导出/导入。

## 核心类/结构体/函数

### VK_EXT_metal_surface
- **`VkMetalSurfaceCreateInfoEXT`**: 包含 `CAMetalLayer` 指针的 Surface 创建信息
- **`vkCreateMetalSurfaceEXT`**: 从 CAMetalLayer 创建 Vulkan Surface

### VK_EXT_metal_objects
- **`VkExportMetalObjectTypeFlagBitsEXT`**: 可导出的 Metal 对象类型标志（Device、CommandQueue、Buffer、Texture、IOSurface、SharedEvent）
- **`VkExportMetalDeviceInfoEXT`**: 导出 MTLDevice
- **`VkExportMetalCommandQueueInfoEXT`**: 导出 MTLCommandQueue
- **`VkExportMetalBufferInfoEXT`** / **`VkImportMetalBufferInfoEXT`**: 导出/导入 MTLBuffer
- **`VkExportMetalTextureInfoEXT`** / **`VkImportMetalTextureInfoEXT`**: 导出/导入 MTLTexture
- **`VkExportMetalIOSurfaceInfoEXT`** / **`VkImportMetalIOSurfaceInfoEXT`**: 导出/导入 IOSurface
- **`vkExportMetalObjectsEXT`**: 导出 Metal 对象的统一入口

## 关键实现逻辑

使用 `#ifdef __OBJC__` 区分 Objective-C 和 C/C++ 编译环境。在 Objective-C 中使用协议类型（如 `id<MTLDevice>`），在 C/C++ 中退化为 `void*`。这使得该头文件可在纯 C++ 代码中安全使用。

## 依赖关系

- `vulkan_core.h` -- Vulkan 核心类型
- Apple Metal 框架类型（`CAMetalLayer`、`MTLDevice` 等，前向声明）
