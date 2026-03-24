# Log.h / Log.cpp

## 文件概述
日志输出流定义，提供分级别的日志流接口。

## 核心类/结构体/函数
- **Loggers**: 日志流集合结构体
  - `d`: DEBUG 级别日志流
  - `e`: ERROR 级别日志流
  - `w`: WARNING 级别日志流
  - `i`: INFORMATION 级别日志流
  - `v`: VERBOSE 级别日志流
- **slog**: 全局日志流实例

## 关键实现逻辑
- 每个级别对应一个 `io::ostream` 引用
- 使用 C++ 流式操作符风格：`slog.d << "message" << io::endl`

## 依赖关系
- `utils/compiler.h`, `utils/ostream.h`
