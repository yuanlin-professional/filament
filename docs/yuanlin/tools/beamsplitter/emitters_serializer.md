# emitters/serializer.go

## 文件概述
C++ 序列化器代码生成器。生成 `Settings_generated.cpp` 和 `Settings_generated.h` 文件，用于 Filament 的视图选项的 JSON 序列化/反序列化。

## 核心函数
- `EmitSerializer()` - 生成 C++ 头文件和实现文件，分别包含序列化读写函数的声明与实现

## 关键实现逻辑
1. 为每个结构体生成 `CppStructReader` 和 `CppStructWriter` 模板代码
2. 为每个枚举生成 `CppEnumReader` 和 `CppEnumWriter` 模板代码
3. 模板扩展函数包括 `trailingcomma`（末尾逗号控制）、`elseif`/`braceif`（条件分支格式化）、`cast`（类型转换）
4. 头文件生成对应的 `HppStruct` 和 `HppEnum` 声明

## 依赖关系
- `beamsplitter/database` - 类型定义数据
- `emitters/serializer.template` - C++ 序列化模板文件
