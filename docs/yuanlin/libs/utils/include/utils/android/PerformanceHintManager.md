# android/PerformanceHintManager.h / PerformanceHintManager.cpp

## 文件概述
Android 性能提示管理器封装，通过 NDK APerformanceHintManager API 向系统提供性能需求提示。

## 核心类/结构体/函数
- **PerformanceHintManager**: Android 性能提示管理器
  - 创建和管理性能提示会话
  - 向系统报告实际工作时长以优化 CPU 频率/调度

## 关键实现逻辑
- 通过 dlsym 动态加载 NDK API，兼容不同 API level
- 仅在 Android 平台有效

## 依赖关系
- Android NDK 性能提示 API
