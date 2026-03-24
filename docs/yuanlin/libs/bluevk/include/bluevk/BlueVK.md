# BlueVK.h / BlueVK.cpp

## 文件概述

BlueVK 是 Filament 渲染引擎的 Vulkan 函数动态加载库的核心模块。头文件 `BlueVK.h` 声明了公共 API 和所有 Vulkan 函数指针的 extern 变量；`BlueVK.cpp` 实现了初始化、实例绑定以及按类别（Loader/Instance/Device）分层加载函数指针的逻辑。两个文件均由 `bluevk-gen.py` 脚本自动生成。

## 核心类/结构体/函数

### 命名空间 `bluevk`

- **`bool initialize()`**: 初始化 BlueVK。调用平台特定的 `loadLibrary()` 加载 Vulkan 共享库，获取 `vkGetInstanceProcAddr` 入口，然后通过 `loadLoaderFunctions()` 加载 Loader 级函数（`vkCreateInstance`、`vkEnumerateInstanceExtensionProperties` 等）。返回 false 表示找不到 Vulkan 库。
- **`void bindInstance(VkInstance instance)`**: 绑定 Vulkan 实例。通过 `vkGetInstanceProcAddr` 加载所有 Instance 级和 Device 级函数指针。

### 函数指针变量
按 Vulkan 版本和扩展分组声明了全部函数指针，例如：
- `VK_VERSION_1_0`: `vkCreateDevice`、`vkCmdDraw`、`vkCreateImage` 等（约 140 个）
- `VK_VERSION_1_1`: `vkBindBufferMemory2`、`vkGetPhysicalDeviceFeatures2` 等
- `VK_VERSION_1_2`: `vkCmdBeginRenderPass2`、`vkGetBufferDeviceAddress` 等
- `VK_VERSION_1_3`: `vkCmdBeginRendering`、`vkCmdPipelineBarrier2` 等
- 各种扩展：`VK_KHR_swapchain`、`VK_EXT_debug_utils`、`VK_KHR_surface` 等

### 平台抽象接口（extern 声明）
- **`bool loadLibrary()`**: 平台相关，加载 Vulkan 共享库。
- **`void* getInstanceProcAddr()`**: 平台相关，获取 `vkGetInstanceProcAddr` 的地址。

### 内部加载函数
- **`loadLoaderFunctions()`**: 加载无需 Instance 的全局函数（如 `vkCreateInstance`）。
- **`loadInstanceFunctions()`**: 加载与物理设备和 Instance 相关的函数。
- **`loadDeviceFunctions()`**: 加载与逻辑设备相关的函数（命令录制、资源管理等）。

## 关键实现逻辑

1. **分层加载策略**: Vulkan 函数按三个层级加载——Loader 级（全局函数）、Instance 级（物理设备查询）、Device 级（命令缓冲操作）。每个层级通过回调函数 `loadcb` 统一调用 `vkGetInstanceProcAddr`。
2. **条件编译**: 所有函数指针按 `#if defined(VK_XXX)` 条件包裹，只有对应的 Vulkan 版本或扩展头被启用时才声明和加载。
3. **平台宏定义**: 头文件根据编译平台自动定义 `VK_USE_PLATFORM_xxx`（Android、iOS、Linux XCB/Xlib/Wayland、macOS Metal、Windows）。
4. **Debug 辅助**: 在非 NDEBUG 模式下，提供各种 Vulkan 枚举类型的 `operator<<` 重载，便于日志输出。

## 依赖关系

- `<vulkan/vulkan.h>` -- Vulkan 标准头文件（类型、常量、PFN 类型定义）
- `<utils/unwindows.h>` -- Filament 工具库，清理 Windows 头文件的宏污染
- `<utils/Panic.h>` -- Filament 断言工具（`BlueVK.cpp` 中使用）
- `<utils/Log.h>` -- Debug 模式下的日志输出
