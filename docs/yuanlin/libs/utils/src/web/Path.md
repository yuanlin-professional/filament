# src/web/Path.cpp

## 文件概述
WebAssembly/Emscripten 平台特定的 Path 实现。

## 核心类/结构体/函数
- Path 类的 Web 平台实现:
  - 文件系统操作在 Web 环境下的适配

## 关键实现逻辑
- 使用 Emscripten 虚拟文件系统 API
- 部分功能在 Web 平台不可用

## 依赖关系
- `utils/Path.h`
