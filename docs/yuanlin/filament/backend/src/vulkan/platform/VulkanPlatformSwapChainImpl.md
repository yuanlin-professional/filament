# VulkanPlatformSwapChainImpl (VulkanPlatformSwapChainImpl.h / VulkanPlatformSwapChainImpl.cpp)

## 文件概述

Vulkan 交换链的具体实现，定义了三种交换链类型：基类 `VulkanPlatformSwapChainBase`、基于表面的 `VulkanPlatformSurfaceSwapChain` 和无头的 `VulkanPlatformHeadlessSwapChain`。管理交换链的图像获取、呈现和重建。

## 核心类/结构体

### `VulkanPlatformSwapChainBase`
交换链抽象基类，持有 VkDevice、VkQueue 和 SwapChainBundle。管理深度图像和颜色图像关联的设备内存（通过 `mMemory` 映射表）。提供 `createImage()` 辅助方法创建 VkImage 并分配设备本地内存。

### `VulkanPlatformSurfaceSwapChain`
基于 VkSurfaceKHR 的真实交换链实现：
- `create()` - 查询表面能力、选择格式（RGBA8/BGRA8, SRGB可选）、创建 VkSwapchainKHR 和深度图像
- `acquire()` - 调用 `vkAcquireNextImageKHR` 获取下一帧图像
- `present()` - 调用 `vkQueuePresentKHR` 提交呈现
- `hasResized()` - 比较当前表面尺寸与交换链尺寸判断是否需要重建
- `recreate()` - 等待队列空闲后销毁并重建交换链
- Android 上支持帧时序查询和合成器时序

### `VulkanPlatformHeadlessSwapChain`
无头交换链（离屏渲染），使用两个手动创建的 VkImage 轮换（双缓冲），acquire 和 present 为空操作。

## 关键实现逻辑

- **信号量池**: SurfaceSwapChain 维护 `IMAGE_READY_SEMAPHORE_COUNT` 个信号量循环使用，避免竞争
- **深度格式选择**: `selectDepthFormat()` 从平台支持的深度格式列表中选择第一个匹配的格式
- **受保护内容**: 支持 `VK_SWAPCHAIN_CREATE_PROTECTED_BIT_KHR` 和 `VK_IMAGE_CREATE_PROTECTED_BIT`
- **子最优处理**: 检测 `VK_SUBOPTIMAL_KHR` 结果并记录日志，避免重复警告
- **销毁安全**: 重建时通过 `vkQueueWaitIdle` 确保旧交换链不再被使用

## 依赖关系

- `vulkan/VulkanContext.h` - 深度格式列表和内存类型选择
- `vulkan/VulkanConstants.h` - FVK_MAX_COMMAND_BUFFERS 等常量
- `vulkan/utils/Definitions.h` - 格式定义
- `vulkan/utils/Helper.h` - VkExtent2D 比较
- `vulkan/utils/Image.h` - 深度格式判断
- `backend/platforms/VulkanPlatform.h` - SwapChainBundle 定义
