# CString.h / CString.cpp

## 文件概述
自定义 C 字符串类，避免 STL 依赖，提供紧凑的堆分配字符串存储。

## 核心类/结构体/函数
- **CString**: 堆分配的可变字符串类
  - 支持 replace、insert、append 操作（左值和右值引用版本）
  - 提供与 `std::string_view` 的互操作
  - 支持异构查找 (heterogeneous lookup)
- **FixedSizeString<N>**: 固定大小栈上字符串
- **to_string<T>()**: 类型到 CString 的转换模板特化

## 关键实现逻辑
- Data 结构体存储在字符串指针前方（mData[-1].length），紧凑布局
- replace 操作：新字符串不超过原长度时原地修改，否则重新分配
- 可选的分配跟踪统计（TRACK_AND_LOG_ALLOCATIONS）
- std::hash 特化使用 DJB2 变体哈希算法

## 依赖关系
- `utils/compiler.h`, `utils/StaticString.h`, `utils/Logger.h`, `utils/ostream.h`
