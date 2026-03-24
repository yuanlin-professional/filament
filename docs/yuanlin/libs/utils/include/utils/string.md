# string.h / string.cpp

## 文件概述
字符串解析工具函数，提供区域无关的浮点数解析。

## 核心类/结构体/函数
- **strtof_c(start, end)**: 区域无关的 strtof 实现，始终使用 C locale 解析浮点数

## 关键实现逻辑
- 避免系统 locale 设置影响浮点数解析（如小数点符号差异）

## 依赖关系
- 无外部 utils 依赖
