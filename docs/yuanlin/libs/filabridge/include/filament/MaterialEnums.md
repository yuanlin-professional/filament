# MaterialEnums.h

## 文件概述
定义 Filament 材质系统的所有公共枚举类型和常量，是材质系统的核心类型定义文件。这些枚举被引擎和材质编译器共同使用。

## 核心类/结构体/函数
- **`MATERIAL_VERSION`** - 当前材质版本号（70），用于材质兼容性检查
- **`Shading`** - 着色模型：UNLIT、LIT、SUBSURFACE、CLOTH、SPECULAR_GLOSSINESS
- **`BlendingMode`** - 混合模式：OPAQUE、TRANSPARENT、ADD、MASKED、FADE、MULTIPLY、SCREEN、CUSTOM
- **`TransparencyMode`** - 透明物体处理方式：DEFAULT、TWO_PASSES_ONE_SIDE、TWO_PASSES_TWO_SIDES
- **`VertexDomain`** - 顶点域：OBJECT、WORLD、VIEW、DEVICE
- **`VertexAttribute`** - 顶点属性枚举，包含位置、切线、颜色、UV、骨骼、自定义属性等
- **`MaterialDomain`** - 材质域：SURFACE、POST_PROCESS、COMPUTE
- **`Property`** - 材质属性（共31个），包括 BASE_COLOR、ROUGHNESS、METALLIC、NORMAL 等 PBR 属性
- **`UserVariantFilterBit`** - 用户变体过滤位掩码，用于控制方向光、动态光照、阴影、蒙皮、雾等变体

## 关键实现逻辑
文件以枚举方式定义了 Filament PBR 材质系统的完整属性集，`Property` 枚举直接对应材质着色器中可设置的表面属性。

## 依赖关系
- `utils/bitset.h`、`utils/BitmaskEnum.h`
- `<stddef.h>`、`<stdint.h>`
