# MeshReader - Filamesh 网格文件读取器

## 文件概述

提供从 filamesh 二进制文件或内存缓冲区加载网格的功能。解析文件头、顶点数据、索引数据、子网格部件和材质信息，创建 Filament 的 `VertexBuffer`、`IndexBuffer` 和 `RenderableManager` 实体。支持压缩数据的解码。

**源文件**: `libs/filameshio/include/filameshio/MeshReader.h`, `libs/filameshio/src/MeshReader.cpp`

## 核心类/结构体

### `MeshReader`
静态工具类，提供三个加载方法：
- `loadMeshFromFile()`: 从文件路径加载，内部分配内存读取后委托给缓冲区版本
- `loadMeshFromBuffer(... MaterialRegistry&)`: 从内存缓冲区加载，使用材质注册表匹配材质
- `loadMeshFromBuffer(... MaterialInstance*)`: 从内存缓冲区加载，所有部件使用同一默认材质

### `MeshReader::MaterialRegistry`
材质实例注册表，内部使用 `std::map<CString, MaterialInstance*>` 存储命名材质。支持注册、注销、查询、遍历操作。未匹配的材质名会回退到名为 "DefaultMaterial" 的默认材质。

### `MeshReader::Mesh`
返回结果结构体，包含 `renderable`（Entity）、`vertexBuffer` 和 `indexBuffer`。

## 关键实现逻辑

1. **文件解析**: 校验魔数 "FILAMESH"，按固定偏移读取 Header、顶点数据、索引数据、Part 数组、材质名称列表
2. **压缩数据解码**: 当 `COMPRESSION` 标志启用时，使用 `meshopt_decodeIndexBuffer` 和 `meshopt_decodeVertexBuffer` 解压。非交错模式下逐属性（positions/tangents/colors/uv0/uv1）分别解压
3. **顶点缓冲区构建**: 使用 `VertexBuffer::Builder` 配置 HALF4 位置、SHORT4 切线（归一化）、UBYTE4 颜色（归一化）、HALF2 或 SHORT2 纹理坐标（取决于 TEXCOORD_SNORM16 标志），可选 UV1
4. **材质匹配**: 按 Part 的材质索引查找材质名称，在 MaterialRegistry 中匹配。未找到时使用默认材质

## 依赖关系

- `<filameshio/filamesh.h>` - 文件格式定义
- `<filament/Engine.h>`, `<filament/VertexBuffer.h>`, `<filament/IndexBuffer.h>`, `<filament/RenderableManager.h>`, `<filament/Material.h>`, `<filament/MaterialInstance.h>` - Filament 渲染对象
- `<meshoptimizer.h>` - 压缩数据解码
- `<utils/EntityManager.h>`, `<utils/Path.h>`, `<utils/Log.h>` - 实体管理和日志
