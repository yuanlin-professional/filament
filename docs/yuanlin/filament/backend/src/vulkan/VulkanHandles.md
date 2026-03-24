# VulkanHandles (VulkanHandles.h / VulkanHandles.cpp)

## 文件概述

定义 Filament 后端所有 Vulkan 资源句柄类型的集合。每种 Filament 硬件资源（如缓冲区、纹理、程序、渲染目标等）都有对应的 Vulkan 实现结构体，它们同时继承 Filament 的 Hw* 基类和 fvkmemory::Resource 引用计数基类。

## 核心类/结构体

### `VulkanDescriptorSetLayout`
描述符集布局，定义最多 4 个描述符集和 25 个绑定点。包含 Bitmask 结构体（UBO/动态UBO/采样器/输入附件位掩码）和 Count 统计结构体。

### `VulkanDescriptorSet`
描述符集实例，持有 VkDescriptorSet 句柄和关联资源引用。

### `VulkanProgram`
着色器程序，持有 VkShaderModule 数组和绑定信息。

### `VulkanRenderTarget`
渲染目标，管理颜色附件、解析附件和深度附件。

### `VulkanBufferObject` / `VulkanIndexBuffer` / `VulkanVertexBuffer` / `VulkanVertexBufferInfo`
各种缓冲区对象，封装 VulkanBufferProxy 并管理缓冲区绑定。

### `VulkanRenderPrimitive`
渲染图元，关联顶点缓冲区和索引缓冲区。

### `VulkanMemoryMappedBuffer`
内存映射缓冲区，用于 CPU-GPU 数据传输。

### `VulkanFramebuffer` / `VulkanRenderPass`
帧缓冲和渲染通道的句柄封装。

## 关键实现逻辑

- **collapsedCount()**: 辅助函数，将顶点/片段阶段的描述符位掩码合并计数
- **双继承模式**: 所有句柄同时继承 HwXxx（Filament 接口）和 fvkmemory::Resource（引用计数）
- **描述符集位掩码**: 使用 64 位位集，低 32 位表示顶点阶段，高 32 位表示片段阶段

## 依赖关系

- `DriverBase.h` - Hw* 硬件资源基类
- `VulkanAsyncHandles.h` / `VulkanBufferCache.h` / `VulkanBufferProxy.h` / `VulkanFboCache.h`
- `VulkanSwapChain.h` / `VulkanTexture.h` / `VulkanCommands.h`
- `vulkan/memory/Resource.h` / `ResourcePointer.h` - 资源管理
- `vulkan/utils/Definitions.h` / `StaticVector.h` - 工具类型
- `backend/Program.h` - 着色器程序定义
