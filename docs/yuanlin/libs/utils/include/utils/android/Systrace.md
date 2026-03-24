# android/Systrace.h / android/Systrace.cpp

## 文件概述
Android 平台的系统追踪实现，封装 ATrace API 用于性能分析。

## 核心类/结构体/函数
- Android 平台特定的 Systrace 实现
- 使用 ATrace NDK API 或 atrace 文件系统接口

## 关键实现逻辑
- 通过写入 `/sys/kernel/debug/tracing/trace_marker` 或使用 NDK ATrace API
- 支持 section/counter 追踪标记

## 依赖关系
- Android NDK ATrace API
