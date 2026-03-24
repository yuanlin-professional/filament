# vk_sdk_platform.h

## 文件概述

`vk_sdk_platform.h` 是 LunarG Vulkan SDK 提供的平台兼容性头文件，处理不同编译器和操作系统之间的差异，提供统一的宏定义。

## 核心类/结构体/函数

### Windows 平台兼容性
- **`NOMINMAX`**: 阻止 Windows SDK 定义 `min`/`max` 宏
- **`inline`**: 在 C 模式下将 `inline` 定义为 `__inline`（MSVC C 编译器兼容）
- **`snprintf`**: 在旧版 MSVC（< VS2015）上映射到 `_snprintf`
- **`strdup`**: 映射到 `_strdup`（MSVC 命名差异）

### `NOEXCEPT` 宏
检测编译器对 C++11 `noexcept` 的支持：
- Clang: 通过 `__has_feature(cxx_noexcept)` 检测
- GCC: 版本 >= 4.6 且启用 C++0x 时支持
- MSVC: 版本 >= VS2015 且启用异常时支持
- 支持时 `NOEXCEPT` 定义为 `noexcept`，否则为空

## 关键实现逻辑

纯预处理器宏文件，不包含任何运行时代码。通过编译器版本检测和特性检测宏，为各平台提供统一的编译环境。

## 依赖关系

- 无外部头文件依赖
