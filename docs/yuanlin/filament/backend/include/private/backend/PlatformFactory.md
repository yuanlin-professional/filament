# PlatformFactory.h

## 文件概述

定义了 `PlatformFactory` 类，提供静态工厂方法用于根据请求的后端类型创建和销毁 `Platform` 实例。

## 核心类

### PlatformFactory

平台工厂类，仅包含静态方法。

## 主要 API

| 方法 | 说明 |
|------|------|
| `create(backendHint)` | 创建指定后端的 Platform。如果请求的后端不可用，会选择最合适的替代并更新 `backendHint`。 |
| `destroy(platform)` | 销毁由 `create()` 创建的 Platform，并将指针置空。 |

## 使用说明

- `create()` 的参数是一个指向 `Backend` 枚举的指针，作为输入/输出参数
- 如果请求的后端在当前平台不可用，工厂会自动选择替代后端并更新该参数
- `destroy()` 通过引用接受指针的指针，确保销毁后置空

## 依赖关系

- `backend/DriverEnums.h` - `Backend` 枚举
- `utils/compiler.h` - `UTILS_PUBLIC` 宏
