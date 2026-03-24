# src/win32/Path.cpp

## 文件概述
Windows 平台特定的 Path 实现。

## 核心类/结构体/函数
- Path 类的 Windows 平台实现:
  - `getCurrentExecutable()`: 使用 `GetModuleFileName`
  - `getTemporaryDirectory()`: 使用 `GetTempPath`
  - `getUserSettingsDirectory()`: 使用 `APPDATA` 环境变量

## 关键实现逻辑
- 使用 Windows API (CreateDirectory, GetFileAttributes 等)
- 路径分隔符处理

## 依赖关系
- `utils/Path.h`, Windows SDK
