# ThermalManager.h

## 文件概述
设备热状态管理的平台适配层。

## 核心类/结构体/函数
- **ThermalManager**: 热管理器类（平台适配别名）

## 关键实现逻辑
- Android 平台使用 `android/ThermalManager.h`（通过 NDK AThermal API）
- 其他平台使用 `generic/ThermalManager.h`（空实现）

## 依赖关系
- `utils/android/ThermalManager.h` 或 `utils/generic/ThermalManager.h`
