# Logger.h

## 文件概述
日志宏接口，提供类似 Abseil 日志 API 的 LOG/DLOG 宏。

## 核心类/结构体/函数
- **LOG(severity)**: 指定级别的日志宏（INFO, WARNING, ERROR, FATAL）
- **DLOG(severity)**: 仅在 Debug 模式生效的日志宏
- **LOG(LEVEL(expr))**: 运行时动态级别日志
- **LogSeverity**: 日志级别枚举 (kInfo, kWarning, kError, kFatal)
- **LogLine<Stream>**: RAII 日志行，析构时自动刷新

## 关键实现逻辑
- 支持 Abseil 日志后端 (FILAMENT_USE_ABSEIL_LOGGING)
- 默认实现基于 utils 自有的 Log 系统
- DLOG 在 Release 模式下编译为 NoopStream，零开销
- LogLine 在析构时自动添加换行并刷新

## 依赖关系
- `utils/Log.h`, `utils/ostream.h`
