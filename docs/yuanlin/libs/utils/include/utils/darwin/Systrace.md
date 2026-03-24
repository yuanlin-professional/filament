# darwin/Systrace.h / darwin/Systrace.cpp

## 文件概述
macOS/iOS 平台的系统追踪实现，使用 os_signpost API。

## 核心类/结构体/函数
- macOS 平台特定的 Systrace 实现
- 使用 Apple os_signpost API 进行性能标记

## 关键实现逻辑
- 通过 os_signpost 在 Instruments 中创建可视化的追踪区间

## 依赖关系
- Apple os/signpost.h
