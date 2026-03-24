# Utility

## 文件概述
Utility 提供在标准实现和扩展实现之间共享的实用函数，包括 Draco 网格解码、meshopt 解压、缓冲区大小计算、数据格式转换以及 cgltf 缓冲区加载。也定义了 URI 数据缓存类型。

## 核心类型定义

- **`UriDataCache`**: `tsl::robin_map<std::string, BufferDescriptor>`，URI 到数据块的映射。
- **`UriDataCacheHandle`**: `std::shared_ptr<UriDataCache>`，共享指针用于跨组件传递。

## 核心函数

- **`decodeDracoMeshes()`**: 解压 KHR_draco_mesh_compression 扩展的网格数据，将解码结果写入对应的 cgltf accessor。
- **`decodeMeshoptCompression()`**: 解码 meshopt 压缩的 buffer view，支持顶点/索引/索引序列三种模式和 octahedral/quaternion/exponential 三种滤波器。
- **`primitiveHasVertexColor()`**: 检查 primitive 是否包含顶点颜色属性。
- **`computeBindingSize()`**: 计算 accessor 对应的缓冲区绑定大小（stride * (count-1) + element_size）。
- **`computeBindingOffset()`**: 计算 accessor 的缓冲区绑定偏移（accessor offset + buffer_view offset）。
- **`convertBytesToShorts()`**: 将 uint8 索引数据转换为 uint16。
- **`requiresConversion()`**: 检查 accessor 的数据类型是否需要格式转换（稀疏数据或 vec3 的某些类型）。
- **`requiresPacking()`**: 检查 accessor 是否需要重新打包（步长不匹配或需要转换）。
- **`loadCgltfBuffers()`**: 加载 cgltf 缓冲区数据，在非文件系统平台（Android/iOS/Web）使用自定义文件读取回调从 URI 缓存中获取数据。

## 依赖关系
- `DracoCache.h`, `FFilamentAsset.h`, `GltfEnums.h`
- `cgltf.h`（包含 CGLTF_IMPLEMENTATION）, `meshoptimizer.h`
- `backend/BufferDescriptor.h`
- `tsl/robin_map.h`
