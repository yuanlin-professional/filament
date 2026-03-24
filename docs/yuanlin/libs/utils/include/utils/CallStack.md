# CallStack.h / CallStack.cpp

## 文件概述
调用栈捕获和符号化工具，用于调试和错误报告。

## 核心类/结构体/函数
- **CallStack**: 调用栈捕获类
  - `unwind(ignore)`: 静态工厂方法，捕获当前线程调用栈
  - `update(ignore)`: 更新当前调用栈
  - `getFrameCount()`: 获取栈帧数量
  - `demangleTypeName()`: C++ 类型名称反修饰
  - `typeName<T>()`: 获取类型的反修饰名称

## 关键实现逻辑
- 使用 `backtrace()` (execinfo.h) 捕获栈帧
- 使用 `dladdr` + `abi::__cxa_demangle` 进行符号化和名称反修饰
- 最多捕获 20 个栈帧

## 依赖关系
- `utils/CString.h`, `utils/compiler.h`, `utils/ostream.h`
