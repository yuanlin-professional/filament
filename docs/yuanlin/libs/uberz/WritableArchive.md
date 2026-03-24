# WritableArchive - 可写归档（构建时序列化）

## 文件概述

提供在构建时创建 Uber 着色器归档的功能。接受预编译材质包和特性规格（spec）描述，将其序列化为紧凑的二进制格式并使用 Zstandard 压缩。是 `ReadableArchive` 的写入对应物，由 uberz 构建工具调用。

**源文件**: `libs/uberz/include/uberz/WritableArchive.h`, `libs/uberz/src/WritableArchive.cpp`

## 核心类/结构体

### `WritableArchive`
- 构造时指定材质数量，内部使用 `FixedCapacityVector<Material>` 存储
- `addMaterial()`: 添加一个命名材质及其预编译包数据
- `addSpecLine()`: 解析一行 spec 文本（支持 `BlendingMode=`、`ShadingModel=`、特性标志 `key=value`）
- `setShadingModel()` / `setBlendingModel()` / `setFeatureFlag()`: 低级 API，不经过文本解析
- `serialize()`: 将所有材质序列化为 zstd 压缩的二进制数据

### `Material` (内部)
- `name`: 材质名称
- `package`: 预编译材质包数据
- `shadingModel`: 着色模型
- `blendingMode`: 混合模式
- `flags`: 特性标志映射（`robin_map<CString, ArchiveFeature>`）

## 关键实现逻辑

1. **Spec 文本解析器** (`addSpecLine`): 手写递归下降解析器，支持注释（#）、`BlendingMode = opaque|transparent|add|masked|fade|multiply|screen|custom`、`ShadingModel = unlit|lit|subsurface|cloth|specularGlossiness`、特性标志 `identifier = unsupported|optional|required`
2. **序列化布局**: 按以下顺序排列：ReadableArchive 头 -> ArchiveSpec 数组 -> ArchiveFlag 数组 -> 标志名称字符串 -> 材质包数据。各字段存储为相对于起始地址的偏移量，以便运行时转换为指针
3. **Zstd 压缩**: Release 模式使用最大压缩级别（构建时不在乎速度），Debug 模式使用最小压缩级别以加速
4. **默认值处理**: 未设置的着色模型和混合模式使用 `INVALID_SHADING_MODEL`/`INVALID_BLENDING`（0xff），表示该材质可用于任意着色/混合模式

## 依赖关系

- `<uberz/ReadableArchive.h>` - 可读归档格式定义（序列化目标）
- `<uberz/ArchiveEnums.h>` - ArchiveFeature 枚举
- `<filament/MaterialEnums.h>` - Shading 和 BlendingMode 枚举
- `<zstd.h>` - Zstandard 压缩库
- `<utils/FixedCapacityVector.h>`, `<utils/CString.h>`, `<utils/Panic.h>`, `<utils/Log.h>` - 工具类
- `<tsl/robin_map.h>` - 高性能哈希映射
