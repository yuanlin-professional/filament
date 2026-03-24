# PIDController.h

## 文件概述
PIDController.h 实现了一个标准的 PID（比例-积分-微分）控制器，用于 Filament 引擎中的自适应控制场景，例如动态分辨率调整中的帧率控制。

## 核心类/结构体/函数
- **PIDController** - PID 控制器
  - `setStandardGains(Kp, Ti, Td)` - 使用标准形式设置增益（Ki = Kp/Ti, Kd = Kp*Td）
  - `setParallelGains(Kp, Ki, Kd)` - 使用并行形式直接设置三个增益
  - `setOutputDeadBand(low, high)` - 设置输出死区（在死区内输出保持为 0）
  - `setIntegralLimits(low, high)` - 设置积分限幅（防止积分饱和）
  - `setOutputLimits(low, high)` - 设置输出限幅
  - `setIntegralInhibitionEnabled()` - 禁用积分项以防止积分饱和
  - `update(measure, target, dt)` - 更新 PID 输出
  - `getError()` / `getIntegral()` / `getDerivative()` - 获取内部状态

## 关键实现逻辑
- `update()` 实现经典 PID 算法：计算误差 -> 积分累加 -> 微分计算 -> 限幅 -> 输出
- 积分饱和防护（anti-windup）通过积分限幅和积分禁止两种机制实现
- 死区功能用于在误差较小时保持输出稳定，避免不必要的微调
- 所有状态变量声明为 `mutable`，允许在 `const` 方法中更新

## 依赖关系
- `math/scalar.h` - 数学标量函数（clamp）

## 被引用关系
- Filament 动态分辨率系统可能使用 PIDController 调整渲染分辨率
