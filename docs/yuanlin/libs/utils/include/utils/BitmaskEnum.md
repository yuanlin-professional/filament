# BitmaskEnum.h

## 文件概述
为枚举类型提供类型安全的位掩码运算符和整数运算符重载。

## 核心类/结构体/函数
- **EnableBitMaskOperators<Enum>**: 特化为 `std::true_type` 以启用位运算符
- **EnableIntegerOperators<Enum>**: 特化为 `std::true_type` 以启用整数比较运算符
- 位运算符: `|`, `&`, `^`, `~`, `|=`, `&=`, `^=`
- 辅助函数: `none()`, `any()`

## 关键实现逻辑
- 使用 SFINAE (`std::enable_if_t`) 确保运算符仅对标记的枚举类型生效
- 通过 `std::underlying_type_t` 转换到底层整数类型执行位运算

## 依赖关系
- 无外部 utils 依赖
