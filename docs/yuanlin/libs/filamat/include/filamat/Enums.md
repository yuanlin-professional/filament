# Enums.h / Enums.cpp

## 文件概述
提供材质编译器中枚举类型与字符串之间的双向转换工具类 `Enums`。用于解析 `.mat` 材质描述文件时，将文本属性名映射为对应的枚举值。

## 核心类/结构体/函数
- **`Enums`** - 模板化的枚举管理类，支持：
  - `isValid<T>(string_view)` - 检查字符串是否为有效的枚举值
  - `toEnum<T>(string_view)` - 将字符串转换为枚举值
  - `toString<T>(T)` - 将枚举值转换为字符串
  - `map<T>()` - 获取完整的字符串到枚举映射表
- 支持的枚举类型：Property、UniformType、SamplerType、SubpassType、SamplerFormat、ParameterPrecision、OutputTarget、OutputQualifier、OutputType、ConstantType、ShaderStageType

## 关键实现逻辑
每种枚举类型维护一个 `unordered_map<string_view, T>` 静态映射表，通过模板特化 `getMap<T>()` 返回对应的映射。`toString()` 通过反向查找 map 实现。

## 依赖关系
- `filamat/MaterialBuilder.h`
