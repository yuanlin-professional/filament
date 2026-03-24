# src/linux/Path.cpp

## 文件概述
Linux 平台特定的 Path 实现，提供文件系统操作和特殊目录查询。

## 核心类/结构体/函数
- Path 类的 Linux 平台实现:
  - `getCurrentExecutable()`: 通过 `/proc/self/exe` 获取
  - `getTemporaryDirectory()`: 返回 `/tmp`
  - `getUserSettingsDirectory()`: 使用 `$HOME`

## 关键实现逻辑
- 使用 POSIX API (stat, mkdir, readdir 等)
- 通过 procfs 获取当前可执行文件路径

## 依赖关系
- `utils/Path.h`
