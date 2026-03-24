# VulkanStreamedImageManager (VulkanStreamedImageManager.h / VulkanStreamedImageManager.cpp)

## 文件概述

流式图像（Streamed Image）管理器，处理来自外部视频流或相机流的动态纹理。当流获取新图像时，自动更新所有绑定了该流纹理的描述符集。

## 核心类/结构体

### `VulkanStreamedImageManager` 类
流式图像管理器：
- `bindStreamedTexture(set, binding, image, samplerParams)` - 绑定流式纹理到描述符集
- `unbindStreamedTexture(set, binding)` - 解除流式纹理绑定
- `onStreamAcquireImage(image, stream)` - 当流获取新图像帧时调用，更新所有关联绑定
- `terminate()` - 清理

### `StreamedTextureBinding` 结构体
记录流式纹理绑定信息：绑定点、纹理引用、描述符集引用和采样器参数。

## 关键实现逻辑

- **绑定追踪**: 维护 `mStreamedTexturesBindings` 向量记录所有活跃的流式纹理绑定
- **批量更新**: 当 `onStreamAcquireImage` 被调用时，遍历所有绑定，对匹配的纹理重新绑定为外部采样纹理
- **委托模式**: 实际的外部纹理绑定操作委托给 VulkanExternalImageManager

## 依赖关系

- `VulkanHandles.h` - VulkanDescriptorSet/VulkanTexture/VulkanStream 类型
- `backend/DriverEnums.h` - SamplerParams
