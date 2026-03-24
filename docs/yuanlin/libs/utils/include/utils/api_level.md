# api_level.h / api_level.cpp

## 文件概述
平台 API 级别查询工具。在 Android 上返回 SDK API level，其他平台返回 0。

## 核心类/结构体/函数
- **api_level()**: 返回当前平台的 API 级别

## 关键实现逻辑
- Android 上通过 `__system_property_get("ro.build.version.sdk")` 读取系统属性
- 使用 `std::call_once` 确保只初始化一次

## 依赖关系
- `utils/compiler.h`
