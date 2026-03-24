# debug.h / debug.cpp

## 文件概述
调试断言工具，提供比标准 assert 更好的诊断信息。

## 核心类/结构体/函数
- **assert_invariant(e)**: 调试模式下的不变量断言宏，Release 模式下为空操作
- **panic()**: 断言失败时调用，输出文件/行号/断言表达式后调用 abort

## 关键实现逻辑
- abort() 函数被标记为 NOINLINE，方便在调试器中设置断点并跳过
- 使用 `UTILS_VERY_LIKELY` 优化正常路径的分支预测

## 依赖关系
- `utils/compiler.h`, `utils/Panic.h`
