# VulkanBlitter (VulkanBlitter.h / VulkanBlitter.cpp)

## 文件概述

提供 Vulkan 图像的 blit（缩放拷贝）和 resolve（MSAA 解析）操作。用于渲染目标之间的图像传输、readPixels 和 copyFrame 等场景。

## 核心类/结构体

### `VulkanBlitter` 类
图像传输管理器：
- `blit(filter, dst, dstRect, src, srcRect)` - 使用 vkCmdBlitImage 执行带缩放的图像拷贝，支持 NEAREST 和 LINEAR 过滤
- `resolve(dst, src)` - 使用 vkCmdResolveImage 将多重采样图像解析为单采样图像
- `terminate()` - 清理资源

## 关键实现逻辑

- **布局转换**: blit 和 resolve 操作前后自动执行图像布局转换（到 TRANSFER_SRC/TRANSFER_DST，完成后恢复）
- **命令缓冲区**: 通过 VulkanCommands 获取当前命令缓冲区录制 blit/resolve 命令
- **格式兼容性**: 依赖物理设备对特定格式的 BLIT_SRC/BLIT_DST 特性支持

## 依赖关系

- `VulkanCommands.h` - 命令缓冲区管理
- `VulkanContext.h` - VulkanAttachment 定义
