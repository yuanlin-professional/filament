# CommonWriter

## 文件概述

`CommonWriter` 是 matdbg 库的共享工具头文件，提供了从 ChunkContainer 读取单个值的模板辅助函数，以及大量枚举值到字符串的转换函数。被 `JsonWriter`、`TextWriter` 和 `ShaderInfo` 共同使用。

**源文件**: `src/CommonWriter.h`, `src/CommonWriter.cpp`

## 核心函数

### 读取辅助
- **`read<T>(container, type, value)`**: 模板函数，从 ChunkContainer 中读取指定 ChunkType 的单个值

### toString 转换函数
覆盖以下枚举类型的字符串转换：
- `Shading` (unlit/lit/subsurface/cloth/specularGlossiness)
- `BlendingMode` (opaque/transparent/add/masked/fade/multiply/screen/custom)
- `Interpolation` (smooth/flat)
- `VertexDomain` (object/world/view/device)
- `MaterialDomain` (surface/post process/compute)
- `CullingMode` (none/front/back/front & back)
- `TransparencyMode`, `VertexAttribute`, `ShaderStage`, `ShaderModel`
- `UniformType`, `SamplerType`, `SubpassType`, `Precision`, `SamplerFormat`, `ConstantType`
- `DescriptorType` (各种采样器组合、uniform buffer、shader storage buffer 等)
- `ShaderStageFlags`, `DescriptorFlags`
- `bool` (true/false)

### `formatVariantString(variant, domain)`
将 Variant 位掩码转为可读的缩写字符串。Surface 域使用 DIR/DYN/SRE/SKN/DEP/PCK/MNT/FOG/S2D/STE 等缩写。PostProcess 域输出 OPA/TRN。Compute 域输出 `---`。

## 关键实现逻辑

`formatVariantString` 中，DEP 位（深度）和非 DEP 位的子变体含义不同：DEP 模式下 PCK 和 MNT 位有效；非 DEP 模式下 FOG 和 S2D 位有效。这反映了 Filament 变体系统的位复用设计。

## 依赖关系

- `filaflat/ChunkContainer.h`, `filaflat/Unflattener.h` -- 数据读取
- `filament/MaterialChunkType.h`, `filament/MaterialEnums.h` -- 枚举定义
- `backend/DriverEnums.h` -- 后端枚举
- `private/filament/Variant.h`, `private/filament/EngineEnums.h` -- Variant 和 PostProcessVariant
