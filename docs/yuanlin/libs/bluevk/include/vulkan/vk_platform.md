# vk_platform.h

## 文件概述

`vk_platform.h` 是 Khronos Group 提供的 Vulkan 平台抽象头文件，定义了跨平台的调用约定宏和基础整数类型。所有 Vulkan 头文件都依赖此文件。

## 核心类/结构体/函数

### 调用约定宏
- **`VKAPI_ATTR`**: 放置在函数返回类型之前。Windows 上为空，Android ARM32 上为 `__attribute__((pcs("aapcs-vfp")))`（硬浮点调用约定）。
- **`VKAPI_CALL`**: 放置在函数返回类型之后。Windows 上为 `__stdcall`，其他平台为空。
- **`VKAPI_PTR`**: 用于函数指针类型中的调用约定修饰。

### 整数类型
在旧版 MSVC（< 1600）上手动定义 `int8_t`、`uint8_t`、`int16_t` 等标准整数类型；其他编译器直接包含 `<stdint.h>`。

## 关键实现逻辑

通过预处理器宏检测目标平台（`_WIN32`、`__ANDROID__`、`__ARM_ARCH` 等），为不同平台设置正确的函数调用约定。特别注意 Android ARM32 平台使用硬浮点 ABI，确保浮点参数通过寄存器传递而非栈。

## 依赖关系

- `<stddef.h>` -- size_t 等（除非定义了 `VK_NO_STDDEF_H`）
- `<stdint.h>` -- 标准整数类型（除非定义了 `VK_NO_STDINT_H`）
