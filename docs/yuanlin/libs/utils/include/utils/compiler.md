# compiler.h

## 文件概述
编译器兼容性宏定义集合，提供跨平台的编译器特性抽象。是 utils 库最底层的头文件。

## 核心类/结构体/函数
- 可见性: `UTILS_PUBLIC`, `UTILS_PRIVATE`
- 分支预测: `UTILS_LIKELY`, `UTILS_UNLIKELY`, `UTILS_VERY_LIKELY`, `UTILS_VERY_UNLIKELY`
- 属性: `UTILS_NOINLINE`, `UTILS_ALWAYS_INLINE`, `UTILS_PURE`, `UTILS_UNUSED`, `UTILS_PACKED`
- 线程: `UTILS_HAS_THREADING`, `UTILS_HAS_HYPER_THREADING`
- 平台指令: `UTILS_PAUSE`, `UTILS_WAIT_FOR_EVENT`, `UTILS_PREFETCH`
- 诊断: `UTILS_WARNING_PUSH`, `UTILS_WARNING_POP`

## 关键实现逻辑
- 通过 `__has_attribute`、`__has_feature`、`__has_builtin` 检测编译器能力
- ARM 平台使用 ACLE 指令 (`__wfi`, `__wfe`, `__yield`)
- x86 平台使用 `rep; nop` 模拟 pause 指令
- 为 Windows 定义 `ssize_t` 类型

## 依赖关系
- 无依赖（最底层头文件）
