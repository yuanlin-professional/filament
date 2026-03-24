# Status.h / Status.cpp

## 文件概述
操作结果状态类，表示操作的成功或失败及原因。

## 核心类/结构体/函数
- **StatusCode**: 状态码枚举 (OK, INVALID_ARGUMENT, INTERNAL, UNSUPPORTED)
- **Status**: 状态类
  - `isOk()`: 检查是否成功
  - `getCode()`: 获取状态码
  - `getMessage()`: 获取消息
  - 工厂方法: `ok()`, `internal()`, `invalidArgument()`, `unsupported()`

## 关键实现逻辑
- 默认构造为 OK 状态
- 消息使用 CString 存储，支持移动语义

## 依赖关系
- `utils/compiler.h`, `utils/CString.h`
