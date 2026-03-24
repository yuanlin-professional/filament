# VulkanSwapChain (VulkanSwapChain.h / VulkanSwapChain.cpp)

## 文件概述

Filament 层面的交换链封装，在平台交换链实现（VulkanPlatformSwapChainBase）之上添加 Filament 特定的纹理管理、图像获取/呈现流程和帧调度回调。

## 核心类/结构体

### `VulkanSwapChain` 结构体
继承 HwSwapChain 和 Resource：
- `present(driver)` - 提交当前帧到平台交换链进行呈现
- `acquire(resized)` - 获取下一帧图像，处理尺寸变化（重建交换链）
- `getCurrentColor()` / `getDepth()` - 获取当前颜色/深度纹理
- `isFirstRenderPass()` / `markFirstRenderPass()` - 追踪帧内首次渲染通道
- `setFrameScheduledCallback()` - 设置帧调度通知回调

## 关键实现逻辑

- **纹理创建**: 基于平台交换链提供的 VkImage 创建 VulkanTexture 包装器，复用 VulkanTexture 的布局转换功能
- **尺寸变化处理**: acquire 时检测平台报告的尺寸变化，触发 flush+wait 后重建交换链
- **信号量管理**: 维护 finishedDrawing 信号量数组用于 GPU-GPU 同步
- **帧调度回调**: 支持客户端注册回调，在 Filament 提交帧时收到通知
- **受保护内容**: 通过平台接口查询交换链是否支持受保护内容
- **无头模式**: mHeadless 标志指示离屏渲染模式

## 依赖关系

- `DriverBase.h` - HwSwapChain 基类
- `VulkanConstants.h` - FVK_MAX_COMMAND_BUFFERS
- `VulkanContext.h` - Vulkan 上下文
- `VulkanTexture.h` - 纹理封装
- `vulkan/memory/Resource.h` - 引用计数基类
- `backend/platforms/VulkanPlatform.h` - 平台交换链接口
