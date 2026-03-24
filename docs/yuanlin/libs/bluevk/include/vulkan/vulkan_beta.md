# vulkan_beta.h

## 文件概述

`vulkan_beta.h` 是 Khronos Group 由 Vulkan XML API Registry 自动生成的 Beta 扩展头文件，包含尚未正式发布的 Vulkan 扩展定义。BlueVK 通过定义 `VK_ENABLE_BETA_EXTENSIONS` 宏来启用这些扩展。

## 核心类/结构体/函数

### VK_KHR_video_queue
- **`VkVideoSessionKHR`** / **`VkVideoSessionParametersKHR`**: 视频会话句柄
- **`VkVideoSessionCreateInfoKHR`**: 视频会话创建信息
- 视频编解码队列管理函数

### VK_KHR_video_decode_queue
- 视频解码相关结构体和命令

### VK_KHR_video_encode_queue
- 视频编码相关结构体和命令

### VK_KHR_portability_subset（如果存在）
- 定义了可移植性子集特性，用于 MoltenVK 等非原生 Vulkan 实现

## 关键实现逻辑

Beta 扩展处于活跃开发阶段，API 可能在未来版本中变更。BlueVK 全局启用 `VK_ENABLE_BETA_EXTENSIONS`，确保能使用视频编解码等前沿功能。

## 依赖关系

- `vulkan_core.h` -- Vulkan 核心类型
- `vk_video/` 目录下的视频编解码标准头文件
