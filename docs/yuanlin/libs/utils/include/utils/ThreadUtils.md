# ThreadUtils.h / ThreadUtils.cpp

## 文件概述
线程工具类，提供线程标识查询功能。

## 核心类/结构体/函数
- **ThreadUtils**: 静态工具类
  - `getThreadId()`: 获取当前线程 ID
  - `isThisThread(id)`: 检查给定 ID 是否是当前线程

## 关键实现逻辑
- 使用 `std::this_thread::get_id()` 获取线程标识

## 依赖关系
- 无外部 utils 依赖
