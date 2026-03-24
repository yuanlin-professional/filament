# Systrace.h

## 文件概述
系统追踪宏定义（已弃用），用于 Filament 内部的性能追踪标记。

## 核心类/结构体/函数
- **SYSTRACE_TAG_***: 追踪标签常量 (NEVER, ALWAYS, FILAMENT, JOBSYSTEM)
- **SYSTRACE_*** 宏: 追踪区间标记宏

## 关键实现逻辑
- 作为公共 API 已弃用，建议使用 Perfetto SDK
- Android 使用 android/Systrace.h，macOS 使用 darwin/Systrace.h
- 通过 SYSTRACE_TAG 宏控制追踪粒度

## 依赖关系
- `utils/android/Systrace.h` 或 `utils/darwin/Systrace.h`（平台相关）
