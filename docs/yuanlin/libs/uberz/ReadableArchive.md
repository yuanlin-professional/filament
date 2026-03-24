# ReadableArchive - 可读归档（运行时解析）

## 文件概述

定义了 Uber 着色器归档的运行时可读二进制格式。该格式设计为"零解析"（parse-free），客户端只需将字对齐的数据块强制转换为 `ReadableArchive` 指针，然后调用偏移量转指针函数即可使用。由 gltfio 在运行时使用。

**源文件**: `libs/uberz/include/uberz/ReadableArchive.h`, `libs/uberz/src/ReadableArchive.cpp`

## 核心类/结构体

### `ReadableArchive`
归档根结构（24 字节）：
- `magic`: 魔数标识
- `version`: 版本号
- `specsCount`: 材质规格数量
- `specs` / `specsOffset`: 指向 ArchiveSpec 数组的指针或偏移量（联合体）

### `ArchiveSpec`
材质规格（24 字节）：
- `shadingModel`: 着色模型（Shading 枚举）
- `blendingMode`: 混合模式（BlendingMode 枚举）
- `flagsCount`: 特性标志数量
- `packageByteCount`: 预编译材质包大小
- `flags` / `flagsOffset`: 指向 ArchiveFlag 数组的指针或偏移量
- `package` / `packageOffset`: 指向材质包数据的指针或偏移量

### `ArchiveFlag`
特性标志（16 字节）：
- `name` / `nameOffset`: 特性名称字符串指针或偏移量
- `value`: ArchiveFeature 值（UNSUPPORTED/OPTIONAL/REQUIRED）

## 关键实现逻辑

1. **convertOffsetsToPointers()**: 将二进制数据中的偏移量原地转换为指针。以 `uint64_t*` 为基址，将 specs、flags、package、name 的偏移量逐层转换为实际内存地址
2. **编译期大小验证**: 使用 `static_assert` 确保各结构体的大小符合预期（ReadableArchive=24, ArchiveSpec=24, ArchiveFlag=16）
3. 哨兵值 `INVALID_SHADING_MODEL`(0xff) 和 `INVALID_BLENDING`(0xff) 用于表示"未指定"

## 依赖关系

- `<uberz/ArchiveEnums.h>` - ArchiveFeature 枚举
- `<filament/MaterialEnums.h>` - Shading 和 BlendingMode 枚举
- `<utils/debug.h>` - 断言
