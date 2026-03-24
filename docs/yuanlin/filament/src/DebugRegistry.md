# DebugRegistry.cpp

## 文件概述

`DebugRegistry` 类的公共 API 实现文件。提供运行时调试属性的注册、查询和修改功能，支持 `bool`、`int`、`float`、`float2`、`float3`、`float4` 等类型。所有方法通过 `downcast` 委托给内部实现。

## 核心类/结构体/函数

### `DebugRegistry` 类方法

- **`hasProperty(const char* name)`** -- 检查指定名称的调试属性是否存在。
- **`setProperty(const char* name, T v)`** -- 设置调试属性值，支持 `bool`、`int`、`float`、`float2`、`float3`、`float4` 类型重载。
- **`getProperty(const char* name, T* v)`** -- 获取调试属性值。
- **`getPropertyAddress(const char* name)`** -- 获取属性的内存地址指针（可变和常量版本）。
- **`getDataSource(const char* name)`** -- 获取属性的数据源信息。

## 关键实现逻辑

标准的 `downcast` 代理模式实现，所有功能逻辑在 `details/DebugRegistry.h` 中的 `FDebugRegistry` 类实现。公共层仅做类型安全的转发。

## 依赖关系

- `details/DebugRegistry.h` -- 内部实现类
- `math/vec2-4.h` -- 向量类型

## 被引用关系

通过 `Engine::getDebugRegistry()` 获取实例，被调试工具和开发者用于在运行时调整渲染参数（如各种渲染特性的开关和数值参数）。
