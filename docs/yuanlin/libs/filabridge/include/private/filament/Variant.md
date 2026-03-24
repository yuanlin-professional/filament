# Variant.h / Variant.cpp

## 文件概述
定义和实现 Filament 的材质变体（Variant）系统。变体是材质着色器的不同编译配置组合，通过 8 位掩码编码方向光、动态光照、阴影接收、蒙皮、深度、雾、VSM、立体渲染等特性，最多 256 种组合（实际有效 96 种）。

## 核心类/结构体/函数
- **`Variant`** - 变体结构体（8 位 key）
  - 位标志：DIR(0x01)、DYN(0x02)、SRE(0x04)、SKN(0x08)、DEP(0x10)、FOG/PCK(0x20)、S2D/MNT(0x40)、STE(0x80)
  - `filterVariantVertex()` / `filterVariantFragment()` - 过滤出仅影响顶点/片段着色器的变体位
  - `filterVariant()` - 根据光照模型过滤无效变体组合
  - `filterUserVariant()` - 根据用户过滤掩码裁剪变体
  - `isValidDepthVariant()` / `isValidStandardVariant()` / `isSSRVariant()` 等判断方法
- **`VariantUtils`** 命名空间 - 提供预计算的变体列表：
  - `getLitVariants()` / `getUnlitVariants()` / `getDepthVariants()` / `getPostProcessVariants()`

## 关键实现逻辑
- 标准变体 128 种（DEP=0），去除无效组合后 84 种；深度变体 16 种，去除无效后 12 种；共 96 种有效变体
- 变体分为标准（DEP=0）和深度（DEP=1）两大类；部分位在两类中含义不同（FOG/PCK、S2D/MNT）
- SSR 变体使用保留空间中的特殊组合 `S2D|SRE`
- 编译时通过 constexpr 函数验证变体计数正确性（96 有效 + 160 保留 = 256）

## 依赖关系
- `filament/MaterialEnums.h`
- `utils/compiler.h`、`utils/bitset.h`、`utils/Slice.h`
- `private/filament/EngineEnums.h`
