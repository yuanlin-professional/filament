# architecture.h / architecture.cpp

## 文件概述
平台架构相关常量和工具函数。

## 核心类/结构体/函数
- **CACHELINE_SIZE**: 缓存行大小常量，固定为 64 字节
- **arch::getPageSize()**: 获取系统内存页大小

## 关键实现逻辑
- Unix/macOS 使用 `sysconf(_SC_PAGESIZE)`
- Windows 使用 `GetSystemInfo` 获取页大小
- 其他平台默认返回 4096

## 依赖关系
- 无外部 utils 依赖
