# VulkanPlatform.h

## 文件概述

定义了 `VulkanPlatform` 类，是 Vulkan 图形后端的平台抽象基类。提供了 Vulkan 实例/设备创建、SwapChain 管理、同步、外部图像等功能的接口。支持高度自定义，包括 GPU 选择、设备创建和交换链行为。

## 核心类

### VulkanPlatform (继承自 Platform)

**内部类型**:
- `VulkanSharedContext` - Vulkan 共享上下文（Instance/Device/Queue）
- `SwapChainBundle` - 交换链图像集合（颜色/深度/格式/尺寸）
- `ImageSyncData` - 图像同步数据（索引 + 信号量）
- `Customization` - 平台自定义选项
- `ExternalImageMetadata` - 外部图像元数据
- `ExternalYcbcrFormat` - YCbCr 外部格式
- `ExtensionSet` - Vulkan 扩展集合

### Customization

| 字段 | 说明 |
|------|------|
| `gpu.deviceName` / `gpu.index` | GPU 选择偏好 |
| `isSRGBSwapChainSupported` | sRGB 交换链支持（默认 true） |
| `flushAndWaitOnWindowResize` | 窗口调整时等待命令完成 |
| `transitionSwapChainImageLayoutForPresent` | 呈现前转换图像布局 |

## 主要 API

### 交换链管理
| 方法 | 说明 |
|------|------|
| `createSwapChain(nativeWindow, flags, extent)` | 创建交换链 |
| `destroy(handle)` | 销毁交换链 |
| `getSwapChainBundle(handle)` | 获取交换链图像信息 |
| `acquire(handle, outSyncData)` | 获取下一帧图像 |
| `present(handle, index, semaphore)` | 呈现图像 |
| `hasResized(handle)` / `recreate(handle)` | 调整大小处理 |

### Vulkan 对象查询
| 方法 | 说明 |
|------|------|
| `getInstance()` / `getDevice()` / `getPhysicalDevice()` | Vulkan 核心对象 |
| `getGraphicsQueue()` / `getGraphicsQueueFamilyIndex()` | 图形队列 |

### 可重写的创建方法
| 方法 | 说明 |
|------|------|
| `createVkInstance(createInfo)` | 自定义 VkInstance 创建 |
| `selectVkPhysicalDevice(instance)` | 自定义 GPU 选择 |
| `createVkDevice(createInfo)` | 自定义 VkDevice 创建 |
| `getSwapchainInstanceExtensions()` | 交换链所需实例扩展（纯虚） |
| `createVkSurfaceKHR(nativeWindow, instance, flags)` | 创建 Surface（纯虚） |

## 依赖关系

- `backend/Platform.h` / `backend/CallbackHandler.h` / `backend/DriverEnums.h`
- `bluevk/BlueVK.h` - Vulkan 加载器
- `utils/CString.h` / `utils/FixedCapacityVector.h` / `utils/PrivateImplementation.h`
