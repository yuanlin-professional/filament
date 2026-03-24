# android/ThermalManager.h / android/ThermalManager.cpp

## 文件概述
Android 平台的热管理实现，监测设备温度状态并提供热限流回调。

## 核心类/结构体/函数
- **ThermalManager**: Android 热管理器
  - 通过 NDK AThermal API 获取设备热状态
  - 注册热状态变化回调

## 关键实现逻辑
- 使用 Android NDK AThermal_* API
- 动态加载以兼容低版本 API

## 依赖关系
- Android NDK Thermal API
