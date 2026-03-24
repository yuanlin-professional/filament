# Path.h / Path.cpp

## 文件概述
跨平台文件路径操作类，提供路径解析、拼接、文件系统查询等功能。

## 核心类/结构体/函数
- **Path**: 文件路径类
  - `exists()`, `isFile()`, `isDirectory()`: 文件系统查询
  - `getParent()`, `getName()`, `getExtension()`: 路径分析
  - `getAbsolutePath()`, `getCanonicalPath()`: 路径规范化
  - `concat()`, `split()`: 路径操作
  - `mkdir()`, `mkdirRecursive()`, `unlinkFile()`: 文件系统操作
  - `getCurrentDirectory()`, `getCurrentExecutable()`: 静态工具方法
  - `getTemporaryDirectory()`, `getUserSettingsDirectory()`: 特殊目录

## 关键实现逻辑
- 路径规范化处理 `.`、`..` 和多余的 `/`
- 平台特定实现分布在 linux/Path.cpp、web/Path.cpp、win32/Path.cpp
- 内部使用 std::string 存储

## 依赖关系
- `utils/compiler.h`
