# ostream.h / ostream.cpp

## 文件概述
自定义输出流实现，用于 Filament 内部日志和调试输出，避免直接依赖 std::ostream。

## 核心类/结构体/函数
- **io::ostream**: 抽象输出流基类
  - 支持所有基本类型的 `operator<<` 重载
  - `dec()` / `hex()`: 切换数字输出格式
  - `setConsumer()`: 设置自定义日志消费者
  - `Buffer`: 内部可增长缓冲区
- 流操作符: `io::hex`, `io::dec`, `io::endl`, `io::flush`
- 支持 bitset32 和 std::string 的输出

## 关键实现逻辑
- 使用 PrivateImplementation 模式隐藏实现细节
- Buffer 按需增长，避免不必要的内存分配
- flush 是纯虚函数，由具体子类（如 sstream、日志流）实现

## 依赖关系
- `utils/bitset.h`, `utils/compiler.h`, `utils/PrivateImplementation.h`
