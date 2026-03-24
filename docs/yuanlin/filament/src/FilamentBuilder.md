# FilamentBuilder.cpp

## 文件概述

实现 `builderMakeName` 辅助函数，用于在 Filament 的 Builder 模式中安全地设置资源名称。

## 核心类/结构体/函数

### `builderMakeName(ImmutableCString& outName, const char* name, size_t len)` 函数

将传入的 C 字符串名称截断到最大 128 字符，并构造为不可变字符串 `ImmutableCString` 存储到输出参数中。如果 `name` 为 `nullptr` 则不做任何操作。

## 关键实现逻辑

- **长度限制**：名称最大长度被限制为 128 字节（`std::min(len, size_t{128u})`），防止过长的名称占用过多内存。
- **空指针安全**：对 `nullptr` 输入提前返回。
- **不可变字符串**：使用 `utils::ImmutableCString` 存储名称，保证创建后不会被意外修改。

## 依赖关系

- `filament/FilamentAPI.h` -- Builder API 声明
- `utils/ImmutableCString.h` -- 不可变字符串类型

## 被引用关系

被各种 Builder 类的 `name()` 方法内部调用（如 `Texture::Builder::name()`、`Material::Builder::name()` 等），用于设置 GPU 资源的调试名称。
