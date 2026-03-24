# UbershaderProvider

## 文件概述
UbershaderProvider 是 MaterialProvider 的预编译 ubershader 实现。它从预构建的材质归档中加载少量复杂的片段着色器，不需要运行时生成着色器，加载速度快但着色器可能不如 JitShaderProvider 精简。

## 核心类

- **`UbershaderProvider`**（匿名命名空间内）: 继承 MaterialProvider，内部使用 ArchiveCache 管理预编译材质。
  - `needsDummyData()` 返回 true（ubershader 需要虚拟属性数据来满足统一的顶点布局）
  - 将 MaterialKey 转换为 ArchiveRequirements 来查找合适的 ubershader

## 关键实现逻辑

- **配置预处理**: `prepareConfig()` 处理 ubershader 模式下的特性冲突，例如 volume+sheen 不兼容、clearcoat 与 transmission/volume/sheen/IOR 不兼容、specularGlossiness 与 specular 不兼容等，打印警告并禁用冲突特性。
- **需求转换**: 从 MaterialKey 提取 shadingModel（UNLIT/LIT）、blendingMode（OPAQUE/MASKED/TRANSPARENT）和特性标志映射（hasBaseColorTexture、hasClearCoat 等），传递给 ArchiveCache 进行匹配。
- **特性映射**: 使用 ArchiveCache 的 FeatureMap 确定材质实例可用的纹理参数。

## 依赖关系
- `gltfio/MaterialProvider.h`
- `ArchiveCache.h`
- `filament/MaterialInstance.h`, `filament/Texture.h`, `filament/TextureSampler.h`
- `math/mat4.h`
- `utils/Log.h`
