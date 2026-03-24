# ProgramSpecialization.h / ProgramSpecialization.cpp

## 文件概述
ProgramSpecialization 定义了一个程序特化标识，用于唯一标识一个编译后的着色器程序。它包含所有可能导致不同编译结果的属性，用作全局程序缓存的键。

## 核心类/结构体/函数
- **ProgramSpecialization** - 程序特化标识结构体
  - `materialCrc32` - 材质文件的 CRC32 校验和
  - `variant` - 着色器变体
  - `specializationConstants` - 特化常量的切片引用
  - `hash()` - 计算哈希值（组合三个成员的哈希）
  - `operator==` - 相等性比较

## 关键实现逻辑
- 哈希计算使用 `utils::hash::combine_fast()` 依次组合三个字段
- 相等性比较先检查指针相同（快速路径），再逐字段比较
- 目前使用 CRC32 而非 cacheId 作为材质标识，因为运行时代码生成可能导致相同 cacheId 对应不同的着色器程序
- 提供了 `std::hash` 特化，使其可直接用作标准库容器的键

## 依赖关系
- `backend/Program.h` - 特化常量类型
- `private/filament/Variant.h` - 变体类型
- `utils/Hash.h` - 哈希工具

## 被引用关系
- `MaterialCache` - 作为全局程序缓存 `RefCountedMap` 的键
- `MaterialDefinition` - 在编译和缓存查找中构造 ProgramSpecialization
- `LocalProgramCache` - 在 `prepareProgramSlow()` 中构造 ProgramSpecialization
