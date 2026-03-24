# JitShaderProvider

## 文件概述
JitShaderProvider 是 MaterialProvider 的运行时着色器生成实现。它在运行时使用 filamat 库组合 GLSL 着色器代码，根据 MaterialKey 动态生成精简的材质。虽然构建成本较高，但生成的着色器更加高效。

## 核心类

- **`JitShaderProvider`**: 继承 MaterialProvider，维护 MaterialKey -> Material* 的 robin_map 缓存。
  - 使用 MurmurHash 对 MaterialKey 进行哈希
  - 支持可选的着色器优化和变体过滤（directionalLighting、dynamicLighting、shadowReceiver、skinning、vsm、fog、ssr、stereo）
  - `needsDummyData()` 返回 false（不需要虚拟属性数据）

## 关键实现逻辑

- 根据 MaterialKey 中的特性标志动态组合着色器字符串，使用 `processShaderString()` 替换 UV 占位符。
- 通过 `filamat::MaterialBuilder` 构建 Material 对象。
- 对已存在的 MaterialKey 直接从缓存返回。
- 支持所有 PBR 扩展：clearcoat、transmission、sheen、volume、specular、IOR、dispersion 等。

## 依赖关系
- `gltfio/MaterialProvider.h`
- `filamat/MaterialBuilder.h`
- `filament/MaterialEnums.h`
- `utils/Hash.h`
- `tsl/robin_map.h`
