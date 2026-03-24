# Panic.h / Panic.cpp

## 文件概述
错误处理框架，提供前置条件/后置条件/算术错误的检测、报告和处理机制。

## 核心类/结构体/函数
- **Panic**: 错误基类接口
  - `what()`, `getType()`, `getReason()`, `getFunction()`, `getFile()`, `getLine()`
  - `setPanicHandler()`: 设置自定义错误处理回调
- **TPanic<T>**: 模板实现类，提供 `panic()` 静态方法
- **PreconditionPanic**: 前置条件违反
- **PostconditionPanic**: 后置条件违反
- **ArithmeticPanic**: 算术错误
- 宏: `FILAMENT_CHECK_PRECONDITION`, `FILAMENT_CHECK_POSTCONDITION`, `FILAMENT_CHECK_ARITHMETIC`
- 宏: `ASSERT_PRECONDITION`, `ASSERT_POSTCONDITION`, `ASSERT_DESTRUCTOR`

## 关键实现逻辑
- 使用 C++ 异常分离错误检测和错误处理
- PanicStream 提供流式错误消息构建
- 析构函数中使用 ASSERT_DESTRUCTOR 避免抛异常
- 支持 Abseil 日志后端 (FILAMENT_PANIC_USES_ABSL)

## 依赖关系
- `utils/CallStack.h`, `utils/compiler.h`, `utils/CString.h`, `utils/sstream.h`
