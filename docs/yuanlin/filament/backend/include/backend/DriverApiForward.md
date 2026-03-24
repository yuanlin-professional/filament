# DriverApiForward.h

## 文件概述

提供 `DriverApi` 类型的前向声明。`DriverApi` 是 `CommandStream` 的类型别名，用于在不引入完整 `CommandStream` 定义的情况下引用驱动 API 类型。

## 核心定义

```cpp
class CommandStream;
using DriverApi = CommandStream;
```

`DriverApi` 实际上就是 `CommandStream`，这个别名使得上层代码可以通过语义更清晰的名称使用命令流接口。

## 用途

该文件被广泛包含在需要引用 `DriverApi` 类型但不需要完整定义的头文件中，减少编译依赖。

## 依赖关系

无外部依赖。
