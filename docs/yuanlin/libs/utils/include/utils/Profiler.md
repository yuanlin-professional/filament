# Profiler.h / Profiler.cpp

## 文件概述
Linux 性能计数器封装，用于精确测量 CPU 周期、指令数、缓存命中率等硬件指标。

## 核心类/结构体/函数
- **Profiler**: 性能分析器类
  - 支持读取 CPU 周期、指令数、缓存引用/缺失、分支/分支缺失等计数器
  - `start()` / `stop()` / `reset()`: 控制计数器
  - `readCounters()`: 读取硬件性能计数器

## 关键实现逻辑
- Linux 上使用 `perf_event_open` 系统调用访问硬件性能计数器
- 非 Linux 平台为空实现
- 使用 chrono 提供时间测量后备方案

## 依赖关系
- `utils/compiler.h`
