# unwindows.h

## 文件概述
Windows 头文件清理工具，取消 Windows.h 定义的常见宏污染。

## 核心类/结构体/函数
- 取消定义 Windows.h 中与 Filament 冲突的宏（如 `near`, `far`, `ERROR` 等）

## 关键实现逻辑
- 通过 `#undef` 清除 Windows SDK 宏定义对命名空间的污染

## 依赖关系
- 无外部 utils 依赖
