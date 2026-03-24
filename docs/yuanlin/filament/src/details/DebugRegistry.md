# DebugRegistry

## 文件概述

`FDebugRegistry` 是 `DebugRegistry` 公共 API 的私有实现类，提供运行时调试属性的注册、读取和写入机制。允许引擎内部模块将调试变量注册为可观测属性，外部工具可通过名称动态查询和修改这些属性，同时支持属性变更回调和数据源注册。

## 核心类/结构体

### `FDebugRegistry`
- 继承自 `DebugRegistry`
- 支持的属性类型枚举：`BOOL`、`INT`、`FLOAT`、`FLOAT2`、`FLOAT3`、`FLOAT4`
- 使用 `std::unordered_map<std::string_view, PropertyInfo>` 存储属性映射
- 支持惰性数据源（通过 `Invocable<DataSource()>` 延迟创建）

### `PropertyInfo`
- `std::pair<void*, std::function<void()>>` -- 属性地址指针 + 变更回调函数

## 关键实现逻辑

- **属性注册** -- `registerProperty()` 通过模板重载支持多种类型，内部统一存储为 `void*`
- **属性修改** -- `setProperty()` 在值变更时触发回调（仅当新值与旧值不同时）
- **数据源** -- 支持直接注册（指针+大小）和惰性注册（工厂函数），首次访问时才创建
- **地址获取** -- `getPropertyAddress()` 在有回调绑定时发出警告，推荐使用 `setProperty()`

## 依赖关系

- `utils/Invocable.h` -- 可调用对象包装器
- `math/mathfwd.h` -- 数学类型前向声明

## 与公共 API 的关系

直接对应 `filament::DebugRegistry`。通过 `Engine::getDebugRegistry()` 获取。在 `FEngine::init()` 中注册阴影调试、froxel 可视化等调试属性，与 matdbg 调试工具配合使用。
