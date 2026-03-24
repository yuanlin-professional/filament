# BlueVK -- Vulkan 函数动态加载器

## 模块概述

BlueVK 是 Filament 的 Vulkan 函数动态加载库。与 BlueGL 类似，它在运行时动态加载 Vulkan 共享库并绑定所有 Vulkan 函数指针，使得 Filament 无需在编译时静态链接 Vulkan SDK。BlueVK 覆盖了 Vulkan 1.0 至 1.3 的完整核心 API，以及大量常用扩展（KHR、EXT、AMD、NV 等）。

## 目录结构

```
libs/bluevk/
  CMakeLists.txt              # 构建配置
  bluevk-gen.py               # 代码生成脚本
  README.md                   # 原始英文文档（更新 Vulkan 头文件说明）
  include/
    bluevk/
      BlueVK.h               # 公共 API 及所有 Vulkan 函数指针声明
    vulkan/                   # Vulkan 官方头文件（来自 Vulkan-Headers）
      vulkan.h
      vulkan_core.h
      ...
    vk_video/                 # Vulkan Video 扩展头文件
  src/
    BlueVK.cpp                # 核心实现（函数指针绑定逻辑）
    BlueVKDarwin.cpp          # macOS 平台动态库加载
    BlueVKLinuxAndroid.cpp    # Linux/Android 平台动态库加载
    BlueVKWindows.cpp         # Windows 平台动态库加载
  tests/
    test_bluevk_sdl.cpp       # SDL 窗口环境下的集成测试
```

## 架构图

```mermaid
flowchart TD
    subgraph 初始化流程
        A[bluevk::initialize] --> B[dlopen libvulkan.so]
        B --> C[加载全局函数指针]
        C --> D[vkCreateInstance]
        D --> E[bluevk::bindInstance]
        E --> F[加载实例级函数指针]
        F --> G[加载设备级函数指针]
    end

    subgraph 运行时调用
        H[应用代码] --> I[bluevk::vkCmdDraw]
        I --> J[Vulkan 驱动]
    end
```

## 核心功能

1. **两阶段初始化** -- `bluevk::initialize()` 首先加载 Vulkan 共享库并获取全局函数（如 `vkCreateInstance`）；创建实例后，`bluevk::bindInstance()` 再加载实例级和设备级函数指针。

2. **完整的 Vulkan 版本覆盖** -- 支持 Vulkan 1.0、1.1、1.2、1.3 的全部核心函数，以及通过条件编译支持各平台特有的 surface 扩展。

3. **丰富的扩展支持** -- 覆盖了 KHR（swapchain、ray tracing 等）、EXT（debug utils、dynamic state 等）、AMD、NV、INTEL 等厂商扩展的函数指针。

4. **跨平台动态库加载**:
   - **Windows**: 加载 `vulkan-1.dll`
   - **macOS**: 加载 `libvulkan.dylib`（通过 MoltenVK）
   - **Linux**: 加载 `libvulkan.so.1`
   - **Android**: 加载 `libvulkan.so`

5. **调试辅助** -- 在 Debug 模式下提供大量 Vulkan 枚举类型的流式输出运算符，方便日志输出和调试。

6. **自动代码生成** -- `bluevk-gen.py` 根据 Vulkan 官方 XML 注册表自动生成 `BlueVK.h` 和 `BlueVK.cpp`，确保与上游 Vulkan Headers 同步。

## 依赖关系

- **utils** -- 使用 `utils::unwindows.h` 处理 Windows 头文件冲突，Debug 模式使用 `utils::Log`
- **math** -- 链接依赖
- **系统依赖**: `libdl`（Linux/macOS）用于动态库加载

## 关键文件说明

| 文件 | 说明 |
|------|------|
| `include/bluevk/BlueVK.h` | 公共 API：`initialize()` 和 `bindInstance()`，以及所有 Vulkan 函数的 `extern` 声明 |
| `include/vulkan/` | Vulkan 官方头文件的本地副本，由 Vulkan-Headers 仓库同步 |
| `src/BlueVK.cpp` | 核心函数指针绑定逻辑，根据 Vulkan 版本和扩展条件加载对应函数 |
| `src/BlueVKDarwin.cpp` | macOS 上通过 `dlopen` 加载 MoltenVK 提供的 Vulkan 实现 |
| `src/BlueVKLinuxAndroid.cpp` | Linux/Android 平台的 Vulkan 共享库加载 |
| `src/BlueVKWindows.cpp` | Windows 平台通过 `LoadLibrary` 加载 Vulkan 运行时 |
| `bluevk-gen.py` | 代码生成脚本，从 Vulkan XML 注册表生成头文件和源文件 |

## 使用方式

```cpp
#include <bluevk/BlueVK.h>

// 第一阶段：加载 Vulkan 库并绑定全局函数
if (!bluevk::initialize()) { return; }

// 创建实例后，绑定实例级和设备级函数
VkInstance instance;
bluevk::vkCreateInstance(&createInfo, nullptr, &instance);
bluevk::bindInstance(instance);
```

更新 Vulkan 头文件时，从 [Vulkan-Headers](https://github.com/KhronosGroup/Vulkan-Headers/tags) 下载最新版本并替换 `include/vulkan/` 目录，然后重新运行 `bluevk-gen.py`。
