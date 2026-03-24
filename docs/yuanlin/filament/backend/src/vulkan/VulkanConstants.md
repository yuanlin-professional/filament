# VulkanConstants (VulkanConstants.h)

## 文件概述

Vulkan 后端的全局常量、调试标志和系统追踪宏定义。通过编译时标志控制各种调试功能的开启/关闭，是调试和性能分析的核心配置文件。

## 核心类/结构体

本文件仅包含宏定义和常量，无类或结构体。

## 关键实现逻辑

### 调试标志位（FVK_DEBUG_*）
每个标志占用一个位，可自由组合：
- `FVK_DEBUG_SYSTRACE` (0x01) - Android systrace
- `FVK_DEBUG_GROUP_MARKERS` (0x02) - GPU 命令分组标记
- `FVK_DEBUG_TEXTURE` (0x04) / `FVK_DEBUG_LAYOUT_TRANSITION` (0x08) - 纹理和布局调试
- `FVK_DEBUG_VALIDATION` (0x40) - Vulkan 验证层
- `FVK_DEBUG_DEBUG_UTILS` (0x8000) - VK_EXT_debug_utils 扩展
- `FVK_DEBUG_RESOURCE_LEAK` (0x10000) - 资源泄漏检测
- `FVK_DEBUG_PROFILING` (0x40000) - 性能分析模式（与其他标志互斥）

### 全局常量
- `VKALLOC` - VkAllocationCallbacks 指针，固定为 nullptr（使用默认分配器）
- `FVK_REQUIRED_VERSION_MAJOR/MINOR` - 最低 Vulkan 版本要求（1.1）
- `FVK_MAX_COMMAND_BUFFERS` (45) - 最大并发命令缓冲区数（3 重缓冲 x 15 渲染通道）
- `FVK_MAX_PIPELINE_AGE` - 未使用管线的淘汰阈值
- `FVK_HANDLE_ARENA_SIZE_IN_MB` (8) - 句柄竞技场大小
- `FVK_RENDERDOC_CAPTURE_MODE` - RenderDoc 捕获模式标志

### systrace 宏
根据配置提供 `FVK_SYSTRACE_CONTEXT/START/END/SCOPE` 和 `FVK_ALWAYS_ON_SYSTRACE_*` 宏。

## 依赖关系

- `utils/Logger.h` - 日志输出宏（FVK_LOGI/LOGW/LOGE/LOGD）
- `private/utils/Tracing.h` - 系统追踪（条件包含）
