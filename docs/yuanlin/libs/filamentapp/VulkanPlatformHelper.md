# VulkanPlatformHelper.h / VulkanPlatformHelper.cpp / VulkanPlatformHelperCommon.h / VulkanPlatformHelper{Android,Apple,Linux,Windows}.cpp

## 文件概述
为示例应用提供 Vulkan 平台的工厂方法，在各操作系统上创建对应的 VulkanPlatform 子类，支持 GPU 设备选择偏好。

## 核心函数

### 公共接口
- `createVulkanPlatform(gpuHintCstr)` - 根据 GPU 偏好提示创建平台实例
- `destroyVulkanPlatform(platform)` - 销毁平台实例

### 内部函数
- `parseGpuHint(gpuHintCstr)` - 解析 GPU 提示字符串，支持数字索引或设备名称

## 关键实现逻辑
- 各平台文件分别继承 `VulkanPlatformAndroid`、`VulkanPlatformApple`、`VulkanPlatformLinux`、`VulkanPlatformWindows`
- 所有平台子类结构一致：`FilamentAppVulkanPlatform` 重写 `getCustomization()` 返回解析后的 GPU 偏好
- GPU 提示若为纯数字则解释为设备索引，否则作为设备名称字符串匹配

## 依赖关系
- `backend/platforms/VulkanPlatform.h` 及各平台子类头文件
- `utils/CString.h`
