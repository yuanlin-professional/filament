# Image (Image.h / Image.cpp)

## 文件概述

提供 Vulkan 图像布局管理、格式判断和布局转换功能。定义了 Filament 内部使用的 `VulkanLayout` 抽象层，将渲染管线的语义布局映射到 Vulkan 原生 VkImageLayout，并管理图像状态转换所需的管线屏障。

## 核心类/结构体

### `VulkanLayout` 枚举
Filament 内部的图像布局抽象（11 种状态）：
- UNDEFINED、STAGING（GENERAL）、FRAG_READ/VERT_READ（SHADER_READ_ONLY_OPTIMAL）
- TRANSFER_SRC/TRANSFER_DST、DEPTH_ATTACHMENT/DEPTH_SAMPLER
- PRESENT、COLOR_ATTACHMENT（GENERAL）、COLOR_ATTACHMENT_RESOLVE

### `VulkanLayoutTransition` 结构体
封装一次图像布局转换所需的全部信息：VkImage、旧布局、新布局和子资源范围。

## 关键实现逻辑

### 布局映射
`getVkLayout()` 将 VulkanLayout 映射到 VkImageLayout。值得注意的是 COLOR_ATTACHMENT 映射到 `VK_IMAGE_LAYOUT_GENERAL` 而非 COLOR_ATTACHMENT_OPTIMAL，因为 Filament 有时需要同时采样和写入同一纹理的不同 mip 级别（如 bloom 效果）。

### 管线屏障转换
`transitionLayout()` 根据新旧布局计算正确的访问掩码和管线阶段：
- 源阶段：根据旧布局确定写入发生的管线阶段和访问类型
- 目标阶段：根据新布局确定读取/写入将发生的阶段
- 使用 `vkCmdPipelineBarrier` 插入 VkImageMemoryBarrier

### 格式判断工具
- `isVkDepthFormat()` / `isVkStencilFormat()` - 通过 `getImageAspect()` 判断格式类型
- `isVKYcbcrConversionFormat()` - 检测需要 YCbCr 采样器转换的多平面格式
- `getImageAspect()` - 根据 VkFormat 返回正确的 VkImageAspectFlags
- `reduceSampleCount()` - 将采样数降低到设备支持的最近值

### 外部图像创建
`createExternalImageFromRaw()` - Android 平台从原始 AHardwareBuffer 指针创建外部图像句柄

## 依赖关系

- `vulkan/VulkanTexture.h` - VulkanTexture 前向声明
- `backend/DriverEnums.h` - 后端枚举类型
- `backend/platforms/VulkanPlatform.h` - ExternalImageHandle 类型
- `bluevk/BlueVK.h` - Vulkan 类型和函数
- `utils/algorithm.h` - clz 函数（用于 reduceSampleCount）
