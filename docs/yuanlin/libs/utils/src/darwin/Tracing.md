# src/darwin/Tracing.cpp

## 文件概述
macOS/iOS 平台的追踪工具实现细节。

## 核心类/结构体/函数
- Darwin 追踪功能的内部实现
- 配合 darwin/Systrace.h 使用

## 关键实现逻辑
- 封装 os_signpost API 的具体调用逻辑

## 依赖关系
- Apple os_signpost API
