# ArchiveEnums - 归档特性枚举定义

## 文件概述

定义了 Uber 着色器归档系统中用于描述材质特性支持级别的枚举类型。该枚举用于标记每个材质对特定渲染特性的支持状态。

**源文件**: `libs/uberz/include/uberz/ArchiveEnums.h`

## 核心类/结构体

### `ArchiveFeature` 枚举 (uint64_t)
- `UNSUPPORTED`: 该材质不支持此特性
- `OPTIONAL`: 该材质可选支持此特性
- `REQUIRED`: 该材质必须启用此特性

## 依赖关系

- `<stdint.h>` - 标准整数类型
