# Compiler.h / Compiler.cpp

## 文件概述
MATC 编译器基类。定义了编译流程模板方法（检查参数 -> 执行编译），以及将编译产物写入二进制 blob 或 C 头文件的功能。

## 核心类
- `Compiler` - 抽象基类
  - `compile()` - 模板方法：`checkParameters()` -> `run()`
  - `writeBlob()` - 将 Package 数据写为二进制文件
  - `writeBlobAsHeader()` - 将 Package 数据写为 C++ 十六进制数组（用于嵌入可执行文件）
  - `writeMat()` - 将处理后的 .mat 文件写出

## 关键实现逻辑
1. C 头文件输出每行 20 个十六进制字节，格式为 `0xNN,`
2. 调试模式下在头文件中注入编译参数和时间戳注释

## 依赖关系
- `filamat/Package` - 编译产物包
- `filament-matp/Config` - 配置接口
