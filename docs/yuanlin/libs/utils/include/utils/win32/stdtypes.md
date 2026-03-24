# win32/stdtypes.h

## 文件概述
Windows 平台的标准类型定义补丁，提供 POSIX 类型兼容。

## 核心类/结构体/函数
- 为 Windows 平台定义缺失的 POSIX 类型（如 ssize_t 等）

## 关键实现逻辑
- 通过 typedef 或 using 声明提供跨平台类型兼容

## 依赖关系
- 无外部 utils 依赖
