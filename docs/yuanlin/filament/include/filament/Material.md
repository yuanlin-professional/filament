# Material.h

## 文件概述

定义材质类，材质描述了表面的视觉外观。Material 从 matc 编译的二进制数据创建，是 MaterialInstance 的模板。

## 核心类/结构体

- **`Material`** - 材质模板，继承自 `FilamentAPI`。从编译后的材质二进制数据创建。
- **`Material::Builder`** - 构建器，加载材质包并设置编译选项。
- **`Material::ParameterInfo`** - 材质参数信息结构体。

## 主要 API

| 方法 | 说明 |
|------|------|
| `Builder::package(payload, size)` | 指定材质二进制数据 |
| `Builder::constant(name, value)` | 设置材质常量参数 |
| `Builder::build(engine)` | 创建材质 |
| `createInstance(name)` | 创建材质实例 |
| `compile(priority, variants, handler, callback)` | 异步编译材质变体 |
| `getName()` | 获取材质名称 |
| `getShading()` / `getBlendingMode()` | 获取着色/混合模式 |
| `getParameterCount()` / `getParameters(...)` | 查询参数信息 |
| `setDefaultParameter(name, value)` | 设置默认实例参数 |
| `getDefaultInstance()` | 获取默认材质实例 |

## 依赖关系

- `filament/Color.h`, `filament/FilamentAPI.h`, `filament/MaterialEnums.h`, `filament/MaterialInstance.h`
- `backend/CallbackHandler.h`, `backend/DriverEnums.h`

## 使用示例

```cpp
auto* mat = Material::Builder()
    .package(matData, matSize)
    .build(*engine);
auto* mi = mat->createInstance();
mi->setParameter("baseColor", RgbType::sRGB, {0.8f, 0.0f, 0.0f});
```
