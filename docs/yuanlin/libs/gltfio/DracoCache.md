# DracoCache

## 文件概述
DracoCache 管理 Draco 压缩网格的解码缓存。当同一个 Draco 网格被多个 primitive 引用时，缓存避免重复解码。DracoMesh 封装了 Draco 解码器的输出，提供面索引和顶点属性的访问接口。

## 核心类/结构体

- **`DracoCache`**: 缓存管理器，使用 `cgltf_buffer_view*` 作为键，通过 `findOrCreateMesh()` 查找或创建 DracoMesh。
- **`DracoMesh`**: 封装解码后的 Draco 网格数据：
  - `decode()`: 静态工厂方法，从压缩数据解码三角形网格
  - `getFaceIndices()`: 将面索引写入 cgltf_accessor 结构
  - `getVertexAttributes()`: 将顶点属性写入 cgltf_accessor 结构
- **`DracoMeshDetails`**: 内部详情，持有 `draco::Mesh` 以及动态分配的 buffer_view 和 buffer。

## 关键实现逻辑

- **条件编译**: 通过 `GLTFIO_DRACO_SUPPORTED` 宏控制 Draco 支持。未启用时所有方法返回 nullptr/false。
- **面索引转换**: `convertFaces()` 模板函数将 Draco 面数据转换为 uint8/uint16/uint32 格式。
- **属性转换**: `convertAttribs()` 模板函数通过 `draco::PointAttribute::ConvertValue()` 将顶点属性转换为目标类型。
- **内存管理**: 解码的缓冲区在 DracoMesh 析构时释放。

## 依赖关系
- `cgltf.h`
- `tsl/robin_map.h`
- `draco/compression/decode.h`（仅在 GLTFIO_DRACO_SUPPORTED 时）
- `utils/Log.h`, `utils/Panic.h`
