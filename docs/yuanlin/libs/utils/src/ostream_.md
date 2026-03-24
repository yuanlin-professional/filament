# src/ostream_.h

## 文件概述
ostream 的内部实现结构体，用于 PrivateImplementation 模式。

## 核心类/结构体/函数
- **ostream_**: io::ostream 的私有实现结构体
  - 存储输出格式状态（十进制/十六进制）
  - 管理消费者回调指针

## 关键实现逻辑
- 作为 PrivateImplementation 的类型参数使用
- 将实现细节与公共头文件分离

## 依赖关系
- `utils/ostream.h`
