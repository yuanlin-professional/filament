# Config

## 文件概述

`Config` 是材质编译器（matc）的配置抽象基类，定义了材质编译所需的全部选项：目标平台、优化级别、输出格式、变体过滤器、宏定义、模板替换映射等。具体的配置实现（如命令行参数解析）由子类提供。

**源文件**: `include/filament-matp/Config.h`

## 核心类/结构体

### `Config`
- **`OutputFormat`** 枚举: `BLOB`（二进制）、`C_HEADER`（C 头文件）、`MAT`（材质文件）
- **`Metadata`** 枚举: `NONE`、`PARAMETERS`（参数反射模式）
- **内嵌类 `Output`**: 输出抽象接口，含 `open/write/close/getOutputStream`
- **内嵌类 `Input`**: 输入抽象接口，含 `open/read/close/getName`
- **`StringReplacementMap`**: `std::map<std::string, std::string, std::less<>>`，支持 string_view 查找的有序映射

### 主要访问器
- `getPlatform()`, `getTargetApi()`, `getOptimizationLevel()` -- 编译目标配置
- `getDefines()`, `getTemplateMap()`, `getMaterialParameters()` -- 宏定义和模板替换
- `getVariantFilter()`, `getFeatureLevel()`, `getWorkarounds()` -- 变体过滤和功能级别
- `printShaders()`, `isDebug()`, `rawShaderMode()` 等 -- 调试选项

## 关键实现逻辑

Config 本身是纯虚基类，所有配置值以 protected 成员变量存储，由子类在构造时填充。`StringReplacementMap` 使用 `std::less<>` 作为比较器，使得可以用 `std::string_view` 直接查找而无需构造 `std::string`。

## 依赖关系

- `filamat/MaterialBuilder.h` -- Platform、TargetApi、Optimization 等类型别名
- `filament/MaterialEnums.h` -- UserVariantFilterMask
- `backend/DriverEnums.h` -- FeatureLevel
