# benchmark_fast.cpp + PerformanceCounters.h - 快速数学函数性能基准测试

## 文件概述

使用 Google Benchmark 框架对 `filament::math::fast` 命名空间中的快速数学函数进行性能测试，并与标准库实现进行对比。`PerformanceCounters.h` 提供硬件性能计数器（CPU 周期、分支预测失误等）的采集封装。

## 核心结构/函数

### PerformanceCounters 类
RAII 风格的性能计数器封装：
- 构造时启动 `utils::Profiler`，采集 CPU 周期和分支预测事件
- `stop()` / 析构时停止采集，将计数器值写入 benchmark state
- 输出指标：C（CPU 周期）、I（指令数）、BPU（分支预测失误）、CPI（每指令周期数）

### 基准测试函数
- `BM_trig<A, T>` -- 三角函数基准，对 1024 个 [-PI, PI] 范围的值进行批量计算
- `BM_func<A, T>` -- 通用函数基准，对 1024 个正浮点值进行批量计算

### 测试用例
| 标准库版本 | 快速版本 | 说明 |
|-----------|---------|------|
| `std::cos` | `fast::cos` | 余弦函数 |
| `std::log2` | `fast::log2` | 以 2 为底的对数 |
| `1/std::sqrt` | `fast::isqrt` | 平方根倒数 |
| `std::pow(x, 2.2f)` | -- | 幂运算对比 |
| -- | `half(float)` | float 到 half 转换 |

每个测试区分标量（Scalar）和向量化（Vector）两种执行模式。

## 依赖关系

- `<benchmark/benchmark.h>` -- Google Benchmark
- `<utils/Profiler.h>` -- Filament 性能分析器
- `<math/fast.h>`, `<math/half.h>`
