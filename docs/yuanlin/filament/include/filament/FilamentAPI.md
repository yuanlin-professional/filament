# FilamentAPI.h

## 文件概述

定义 Filament 公共 API 的基类 `FilamentAPI`，禁止用户直接在栈上或堆上创建/销毁 API 对象，确保对象生命周期由引擎管理。

## 核心类/结构体

- **`FilamentAPI`** - 所有 Filament API 对象的基类。禁止拷贝、移动和堆分配。
- **`BuilderBase<T>`** - Builder 模式的基类模板（别名为 `utils::PrivateImplementation<T>`）。
- **`BuilderNameMixin<Builder>`** - 为 Builder 提供 `name()` 方法的 mixin 类。

## 主要 API

| 方法 | 说明 |
|------|------|
| `BuilderNameMixin::name(name)` | 为对象关联调试名称 |
| `BuilderNameMixin::getName()` | 获取已设置的名称 |

## 依赖关系

- `utils/compiler.h`, `utils/PrivateImplementation.h`
- `utils/ImmutableCString.h`, `utils/StaticString.h`
