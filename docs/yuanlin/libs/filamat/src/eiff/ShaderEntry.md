# ShaderEntry.h

## 文件概述

定义了两种着色器条目数据结构，分别用于文本格式着色器（如 GLSL、MSL、WGSL）和二进制格式着色器（如 SPIR-V）。这些结构是材质编译过程中着色器变体数据的载体。

## 核心结构体

### `TextEntry`

存储文本格式的着色器变体：

| 字段 | 类型 | 说明 |
|------|------|------|
| `shaderModel` | `ShaderModel` | 着色器模型（MOBILE/DESKTOP） |
| `variant` | `Variant` | 材质变体标识 |
| `stage` | `ShaderStage` | 着色器阶段（VERTEX/FRAGMENT/COMPUTE） |
| `shader` | `std::string` | 着色器源代码文本 |

### `BinaryEntry`

存储二进制格式的着色器变体：

| 字段 | 类型 | 说明 |
|------|------|------|
| `shaderModel` | `ShaderModel` | 着色器模型 |
| `variant` | `Variant` | 材质变体标识 |
| `stage` | `ShaderStage` | 着色器阶段 |
| `dictionaryIndex` | `size_t` | 在 `BlobDictionary` 中的索引 |
| `data` | `vector<uint8_t>` | 临时存储的二进制数据，添加到字典后不再使用 |

## 关键实现逻辑

`BinaryEntry` 的 `data` 字段用于在编译阶段临时持有二进制着色器数据（如 SPIR-V 字节码），在调用 `BlobDictionary::addBlob()` 后，`dictionaryIndex` 被赋值，`data` 随即通过 `std::move` 转移。

## 依赖关系

- `<private/filament/Variant.h>` -- 材质变体定义
- `<backend/DriverEnums.h>` -- `ShaderModel`、`ShaderStage` 枚举
- `<string>` / `<vector>` -- 标准库
