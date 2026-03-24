# SystraceProfile

## 文件概述

`SystraceProfile.h` 是一个简单的宏定义头文件，为后端代码提供统一的性能分析标记宏。将 Filament 的追踪系统与后端特定的帧标记名称关联起来。

## 核心类/结构体

本文件不定义类或结构体，仅提供宏定义。

## 关键实现逻辑

- **PROFILE_SCOPE(marker)**: 映射到 `SYSTRACE_NAME(marker)`，在当前作用域内创建性能追踪标记。
- **PROFILE_NAME_BEGINFRAME**: 定义为字符串 `"backend::beginFrame"`，标记帧开始。
- **PROFILE_NAME_ENDFRAME**: 定义为字符串 `"backend::endFrame"`，标记帧结束。

这些宏被各后端驱动在 `beginFrame()` 和 `endFrame()` 中使用，在 systrace/perfetto 等追踪工具中产生可读的帧边界标记。

## 依赖关系

- `private/utils/Tracing.h` - 底层追踪系统（SYSTRACE_NAME 宏定义）
