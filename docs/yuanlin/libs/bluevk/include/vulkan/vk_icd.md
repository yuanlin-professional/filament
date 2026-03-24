# vk_icd.h

## 文件概述

`vk_icd.h` 定义了 Vulkan Loader 与 ICD（Installable Client Driver，可安装客户端驱动）之间的接口协议。该文件由 Khronos/LunarG 提供，用于 Vulkan 驱动开发者实现标准化的 Loader-ICD 交互。

## 核心类/结构体/函数

### 接口版本常量
- **`CURRENT_LOADER_ICD_INTERFACE_VERSION`**: 当前接口版本 6
- **`MIN_SUPPORTED_LOADER_ICD_INTERFACE_VERSION`**: 最低支持版本 0

### 接口函数类型
- **`PFN_vk_icdNegotiateLoaderICDInterfaceVersion`**: Loader-ICD 版本协商
- **`PFN_vk_icdGetInstanceProcAddr`**: ICD 的实例函数查询
- **`PFN_vk_icdGetPhysicalDeviceProcAddr`**: 物理设备扩展查询

### Loader 数据结构
- **`VK_LOADER_DATA`**: 联合体，ICD 对象头部必须预留的 Loader 分发表指针空间
- **`ICD_LOADER_MAGIC`**: 魔数 `0x01CDC0DE`，用于验证 Loader 数据有效性

### WSI 平台枚举与表面结构
- **`VkIcdWsiPlatform`**: 枚举窗口系统平台类型（Wayland、Win32、XCB、Xlib、Android、Metal 等）
- **`VkIcdSurfaceBase`** 及各平台派生结构: ICD 内部的 Surface 表示

## 关键实现逻辑

提供 `set_loader_magic_value()` 和 `valid_loader_magic_value()` 内联函数，用于 ICD 设置和验证对象头部的 Loader 魔数。各平台的 Surface 结构体继承自 `VkIcdSurfaceBase`，包含平台特定的窗口系统句柄。

## 依赖关系

- `"vulkan.h"` -- Vulkan 核心类型定义
- `<stdbool.h>` -- bool 类型
