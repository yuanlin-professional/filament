# downcast.h

## 文件概述

定义 `FILAMENT_DOWNCAST` 宏，用于生成从公共 API 类到内部实现类的安全向下转型（downcast）函数。这是 Filament 实现公共接口与内部实现分离的核心机制。

## 核心类/结构体/函数

### `FILAMENT_DOWNCAST(CLASS)` 宏

为给定的类名 `CLASS` 生成四个重载的 `downcast` 函数：
- `F##CLASS& downcast(CLASS& that)` -- 引用的非 const 转换
- `const F##CLASS& downcast(const CLASS& that)` -- 引用的 const 转换
- `F##CLASS* downcast(CLASS* that)` -- 指针的非 const 转换
- `F##CLASS const* downcast(CLASS const* that)` -- 指针的 const 转换

所有转换使用 `static_cast`，这是安全的，因为 `FBar` 总是继承自 `Bar`。

## 关键实现逻辑

该宏利用 C++ 的 `static_cast` 进行编译期安全的向下转型。约定内部实现类使用 `F` 前缀（如 `Engine` -> `FEngine`），公共类是基类，内部类是派生类。这种设计模式类似于 PIMPL（指向实现的指针），但通过继承而非组合来实现，避免了额外的间接层。

## 依赖关系

无外部依赖。

## 被引用关系

被 Filament 几乎所有的 `.cpp` 实现文件使用。每个公共 API 类（如 `Engine`、`Camera`、`View` 等）的对应 `details/` 头文件中都会调用 `FILAMENT_DOWNCAST(ClassName)`，从而允许公共接口方法通过 `downcast(this)` 访问内部实现。
