# StaticMaterialInfo

## 文件概述

`StaticMaterialInfo.h` 定义了内置静态材质数据的描述结构，用于在编译时嵌入材质二进制数据。

## 核心类/结构体

- **`StaticMaterialInfo`**: 静态材质信息结构。
  - `name`: 材质名称（`string_view`）
  - `data`: 指向编译后的材质二进制数据
  - `size`: 数据大小
  - `constants`: 材质常量列表（`initializer_list<ConstantInfo>`），支持 `int32_t`、`float`、`bool` 三种类型
  - **`ConstantInfo`**: 常量名称和值（`std::variant<int32_t, float, bool>`）

- **`MATERIAL` 宏**: `#define MATERIAL(p, n) p ## _ ## n ## _DATA, size_t(p ## _ ## n ## _SIZE)`，简化材质数据引用。

## 关键实现逻辑

- 材质在构建时由 matc 编译器编译为二进制数据，通过资源嵌入方式链接到引擎中。
- `initializer_list` 的使用允许在声明时内联指定常量值，其内部对象的生命周期与 `initializer_list` 一致。

## 依赖关系

- `<initializer_list>`, `<string_view>`, `<variant>` - C++17 标准库
