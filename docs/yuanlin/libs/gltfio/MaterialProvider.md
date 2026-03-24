# MaterialProvider

## 文件概述
MaterialProvider 是材质提供器的接口及公共实现。它定义了 glTF 材质需求到 Filament Material/MaterialInstance 的映射机制，包含材质键（MaterialKey）的定义、UV 集约束算法和着色器字符串处理。

## 核心类/结构体

- **`MaterialKey`**: 20 字节的 POD 结构体，编码 glTF 材质的所有需求（双面、无光照、顶点色、各纹理槽位、alpha 模式、clearcoat、transmission、sheen、volume、specular 等）。用 MurmurHash 进行哈希。
- **`MaterialProvider`**: 抽象接口，定义 `createMaterialInstance()`、`getMaterial()`、`destroyMaterials()`、`needsDummyData()` 等虚方法。有两个实现：JitShaderProvider 和 UbershaderProvider。
- **`UvMap`**: 8 元素数组，将 glTF 的 texcoord 索引映射到 Filament 的 UV0/UV1。
- **`AlphaMode`**: 枚举，支持 OPAQUE、MASK、BLEND。

## 关键实现逻辑

- **UV 集约束**: `constrainMaterial()` 将 glTF 的任意数量纹理坐标集约束到 Filament 支持的最多 2 个 UV 集，按 glTF 规范定义的优先级降级：baseColor > metallicRoughness > normal > occlusion > emissive > transmission > clearcoat > sheen > volume > specular。
- **着色器字符串处理**: `processShaderString()` 将着色器模板中的 `${color}`、`${normal}` 等占位符替换为实际的 UV 获取函数（`getUV0()`/`getUV1()`/`vec2(0)`）。
- **MaterialKey 比较**: operator== 逐字段比较所有材质属性。

## 依赖关系
- `filament/Engine.h`, `filament/Material.h`, `filament/MaterialInstance.h`
- `utils/FixedCapacityVector.h`, `utils/compiler.h`
