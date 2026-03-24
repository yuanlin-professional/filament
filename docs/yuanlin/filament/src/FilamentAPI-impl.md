# FilamentAPI-impl.h

## 文件概述

尽管是头文件形式，实际充当 `.cpp` 文件，用于显式实例化 `BuilderBase<>` 模板的方法。这是 Filament 构建器模式（Builder Pattern）基础设施的一部分。

## 核心类/结构体/函数

本文件不定义新的类或函数，而是通过包含 `PrivateImplementation-impl.h` 来触发 `BuilderBase` 模板的显式实例化。

## 关键实现逻辑

Filament 使用 PIMPL（Pointer to Implementation）模式实现其 Builder API。`FilamentAPI.h` 声明了 `BuilderBase` 模板，而此文件通过包含 `utils/PrivateImplementation-impl.h` 提供了模板方法的实际定义。这种模式将模板实现从头文件中分离出来，减少编译依赖。

## 依赖关系

- `filament/FilamentAPI.h` -- `BuilderBase` 模板声明
- `utils/PrivateImplementation-impl.h` -- PIMPL 实现细节

## 被引用关系

被所有使用 Builder 模式的资源创建类（如 `BufferObject.cpp`、`Texture.cpp`、`Material.cpp` 等）包含，以获得 Builder 基类的实现。
