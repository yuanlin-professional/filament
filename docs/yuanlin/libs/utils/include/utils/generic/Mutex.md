# generic/Mutex.h

## 文件概述
通用平台的互斥锁实现，基于 std::mutex 的简单封装。

## 核心类/结构体/函数
- **Mutex**: 互斥锁类，封装 `std::mutex`

## 关键实现逻辑
- 直接使用 C++ 标准库实现，适用于非 Android 平台

## 依赖关系
- 无外部 utils 依赖
