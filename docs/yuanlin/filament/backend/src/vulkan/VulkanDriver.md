# VulkanDriver (VulkanDriver.h / VulkanDriver.cpp)

## 文件概述

Vulkan 后端的核心驱动实现，继承自 DriverBase，实现 Filament 渲染后端的全部 API 接口。负责协调所有 Vulkan 子系统（命令缓冲区、管线缓存、描述符集、纹理、缓冲区等）的工作。

## 核心类/结构体

### `VulkanDriver` 类
最终驱动类（final），持有所有 Vulkan 子系统的实例：
- `VulkanCommands` - 命令缓冲区管理
- `VulkanPipelineCache` - 图形管线缓存
- `VulkanDescriptorSetCache` / `VulkanDescriptorSetLayoutCache` - 描述符集管理
- `VulkanPipelineLayoutCache` - 管线布局缓存
- `VulkanFboCache` - 帧缓冲/渲染通道缓存
- `VulkanSamplerCache` - 采样器缓存
- `VulkanBufferCache` - GPU 缓冲区池
- `VulkanStagePool` - 暂存缓冲区池
- `VulkanBlitter` - 图像拷贝/缩放
- `VulkanReadPixels` - 像素回读
- `VulkanQueryManager` - 时间戳查询
- `VulkanSemaphoreManager` - 信号量管理
- `VulkanExternalImageManager` / `VulkanStreamedImageManager` - 外部图像管理
- `fvkmemory::ResourceManager` - 资源生命周期管理

### `DebugUtils` 内部类（条件编译）
封装 VK_EXT_debug_utils 扩展，提供 `setName()` 为 Vulkan 对象设置调试名称。

## 关键实现逻辑

- **静态工厂**: `create()` 静态方法是唯一创建入口，从 VulkanPlatform 获取设备/队列等信息
- **资源创建/销毁**: 通过 `resource_ptr` 管理所有 GPU 资源的生命周期
- **命令提交**: 驱动维护当前活跃的 VulkanCommandBuffer，命令通过它提交到 GPU
- **MAX_RENDERTARGET_ATTACHMENT_TEXTURES**: 定义单个渲染目标最大纹理附件数（颜色 + 解析 + 深度）

## 依赖关系

- 几乎依赖所有 vulkan/ 子模块：VulkanBlitter、VulkanBufferCache、VulkanCommands、VulkanContext、VulkanFboCache、VulkanHandles、VulkanMemory、VulkanPipelineCache 等
- `DriverBase.h` / `private/backend/Driver.h` - 驱动基类接口
- `vulkan/memory/ResourceManager.h` / `ResourcePointer.h` - 资源管理
