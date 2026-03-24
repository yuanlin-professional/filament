# ArchiveCache

## 文件概述
ArchiveCache 管理一组预编译的 Filament ubershader 材质包。它从 zstd 压缩的归档文件中加载材质规格，并根据运行时需求选择最合适的 ubershader。由 UbershaderProvider 内部使用。

## 核心类/结构体

- **`ArchiveCache`**: 缓存类，提供 `load()`、`getMaterial()`、`getDefaultMaterial()`、`destroyMaterials()` 和 `getFeatureMap()` 方法。
- **`ArchiveRequirements`**: 材质需求描述，包含着色模型（Shading）、混合模式（BlendingMode）和特性标志映射（features）。
- **`FeatureMap`**: 从特性名到 ArchiveFeature 的映射（robin_map）。

## 关键实现逻辑

- **加载**: `load()` 使用 zstd 解压归档数据，调用 `convertOffsetsToPointers()` 将偏移转换为指针，初始化材质指针数组。
- **材质查找**: `getMaterial()` 遍历所有 ubershader 规格，检查混合模式和着色模型匹配，然后验证网格所需的所有特性标志在该 ubershader 中是 OPTIONAL 或 REQUIRED。同时检查 REQUIRED 特性是否在网格中存在。首次匹配时懒创建 Material 对象。
- **默认材质**: `getDefaultMaterial()` 返回归档中的第一个材质规格。

## 依赖关系
- `filament/Engine.h`, `filament/Material.h`
- `uberz/ReadableArchive.h`, `uberz/ArchiveEnums.h`
- `utils/CString.h`, `utils/FixedCapacityVector.h`
- `tsl/robin_map.h`, `zstd.h`
