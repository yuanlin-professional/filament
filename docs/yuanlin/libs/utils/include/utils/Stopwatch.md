# Stopwatch.h

## 文件概述
简单的秒表计时工具，记录最小/最大/平均计时，适用于性能测量。

## 核心类/结构体/函数
- **Stopwatch<Clock>**: 秒表模板类
  - `start()` / `stop()`: 开始/结束一次计时
  - `getMinLapTime()` / `getMaxLapTime()` / `getAverageLapTime()`: 获取统计
  - 析构时自动输出统计日志
- **AutoStopwatch<Stopwatch>**: RAII 自动计时器

## 关键实现逻辑
- 使用 std::chrono 进行时间测量
- 每次 stop() 增量更新 min/max/avg 统计
- AutoStopwatch 在构造时 start，析构时 stop

## 依赖关系
- `utils/Log.h`, `utils/ostream.h`
