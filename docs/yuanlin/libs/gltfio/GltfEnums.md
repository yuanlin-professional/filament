# GltfEnums

## 文件概述
GltfEnums.h 提供 cgltf 枚举类型到 Filament 枚举类型的转换函数集合。这些内联函数在 AssetLoader 和 ResourceLoader 中被广泛使用，处理纹理采样器、顶点属性、索引类型和图元类型的转换。

## 核心函数

- **`getWrapMode()`**: cgltf wrap mode -> TextureSampler::WrapMode（REPEAT/MIRRORED_REPEAT/CLAMP_TO_EDGE）
- **`getMinFilter()`**: cgltf min filter -> TextureSampler::MinFilter（支持 6 种 mipmap 模式）
- **`getMagFilter()`**: cgltf mag filter -> TextureSampler::MagFilter（NEAREST/LINEAR）
- **`getVertexAttrType()`**: cgltf attribute type -> VertexAttribute（POSITION/UV0/COLOR/BONE_INDICES/BONE_WEIGHTS）
- **`getCustomVertexAttrType()`**: 自定义索引 -> VertexAttribute::CUSTOM0+
- **`getIndexType()`**: cgltf component type -> IndexBuffer::IndexType（USHORT/UINT）
- **`getPrimitiveType()`**: cgltf primitive type -> PrimitiveType（POINTS/LINES/LINE_STRIP/TRIANGLES/TRIANGLE_STRIP）
- **`getElementType()`**: (cgltf_type, cgltf_component_type) -> (permitType, actualType)。返回两种类型：安全的"允许类型"（跨 GPU/后端通用）和"实际类型"。当两者不一致时需要数据转换。

## 依赖关系
- `filament/IndexBuffer.h`, `filament/RenderableManager.h`, `filament/TextureSampler.h`, `filament/VertexBuffer.h`
- `cgltf.h`
