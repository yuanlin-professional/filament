# VulkanExternalImageManager (VulkanExternalImageManager.h / VulkanExternalImageManager.cpp)

## 文件概述

外部图像（如 Android AHardwareBuffer）的 Vulkan 绑定管理器。处理外部采样器纹理的特殊描述符集布局和绑定逻辑，因为外部采样器需要不可变采样器绑定到描述符集布局中。

## 核心类/结构体

### `VulkanExternalImageManager` 类
外部图像管理器：
- `bindExternallySampledTexture(set, binding, image, samplerParams)` - 将外部采样纹理绑定到描述符集的指定绑定点
- `removeDescriptorSet(set)` - 移除描述符集关联的外部图像绑定
- `terminate()` - 清理资源

### 类型别名
- `SetArray` / `LayoutArray` / `VkLayoutArray` - 描述符集和布局的数组类型

## 关键实现逻辑

- **不可变采样器**: 外部采样器纹理要求将 VkSampler（含 YCbCr 转换）作为不可变采样器绑定到布局中
- **布局重建**: 当外部纹理绑定变化时，可能需要重新创建包含不同不可变采样器的 VkDescriptorSetLayout
- **协调多子系统**: 需要同时操作 VulkanSamplerCache（创建采样器）、VulkanYcbcrConversionCache（YCbCr 转换）、VulkanDescriptorSetLayoutCache（布局重建）和 VulkanDescriptorSetCache（集更新）

## 依赖关系

- `VulkanHandles.h` - VulkanDescriptorSet/VulkanTexture 等类型
- `backend/DriverEnums.h` - SamplerParams
