# VulkanPipelineCache (VulkanPipelineCache.h / VulkanPipelineCache.cpp)

## 文件概述

Vulkan 图形管线缓存，管理 VkPipeline 对象的创建和复用。将光栅化状态、着色器程序、顶点属性布局等组合为管线键值进行缓存，避免重复创建开销巨大的 VkPipeline 对象。

## 核心类/结构体

### `VulkanPipelineCache` 类
管线缓存管理器，核心功能：
- 基于 RasterState + 着色器 + 顶点布局 + 渲染通道等参数查找或创建 VkPipeline
- 支持异步管线编译（通过 CompilerThreadPool）
- 支持管线缓存预热（pipeline cache prewarming）
- 按提交次数（FVK_MAX_PIPELINE_AGE）淘汰未使用的管线

### `RasterState` 结构体
紧凑的位域 POD 结构，编码全部光栅化状态：
- 剔除模式、正面方向、深度偏移、混合使能
- 源/目标颜色/透明度混合因子
- 颜色写入掩码、光栅化采样数、深度截断
- 颜色目标数量、混合方程
- 深度写入、深度比较操作、模板状态

### 管线键（PipelineKey）
组合所有影响 VkPipeline 创建的参数，用作 robin_map 的查找键。

## 关键实现逻辑

- **惰性创建**: 首次使用特定状态组合时才创建 VkPipeline，后续直接从缓存返回
- **视口/裁剪动态**: 视口和裁剪矩形被设为动态状态，不烘焙到管线中
- **淘汰策略**: 管线在 FVK_MAX_PIPELINE_AGE 次提交后如果未再使用则被销毁
- **异步编译**: 支持在后台线程预编译管线，减少主线程卡顿

## 依赖关系

- `CallbackManager.h` / `CompilerThreadPool.h` - 异步编译支持
- `VulkanCommands.h` - 命令缓冲区引用
- `backend/DriverEnums.h` / `backend/TargetBufferInfo.h` - 后端枚举
- `bluevk/BlueVK.h` - Vulkan 类型
- `tsl/robin_map.h` - 高性能哈希表
