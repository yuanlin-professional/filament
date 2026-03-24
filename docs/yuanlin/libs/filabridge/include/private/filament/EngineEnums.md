# EngineEnums.h

## 文件概述
定义 Filament 引擎内部使用的枚举类型和配置常量，包括描述符集绑定点、特化常量 ID、以及各种资源的最大限制值。

## 核心类/结构体/函数
- **`DescriptorSetBindingPoints`** - 描述符集绑定点：PER_VIEW(0)、PER_RENDERABLE(1)、PER_MATERIAL(2)
- **`PerViewBindingPoints`** - 每视图绑定点（共 12 个）：帧 uniform、阴影、灯光、Froxel、IBL、SSAO、SSR、雾等
- **`PerRenderableBindingPoints`** - 每可渲染对象绑定点：对象 uniform、骨骼、变形等
- **`ReservedSpecializationConstants`** - 引擎保留的特化常量 ID（0-11）
- **`PostProcessVariant`** - 后处理变体：OPAQUE、TRANSLUCENT

## 关键实现逻辑
文件定义了引擎的关键配置常量：
- `CONFIG_MAX_LIGHT_COUNT = 255` - 最大灯光数（受 UBO 16KiB 限制）
- `CONFIG_MAX_SHADOWMAPS = 128` - 最大阴影贴图数
- `CONFIG_MAX_SHADOW_CASCADES = 4` - 最大级联阴影数
- `CONFIG_MAX_BONE_COUNT = 256` - 最大骨骼数
- `CONFIG_MAX_MORPH_TARGET_COUNT = 256` - 最大变形目标数
- `CONFIG_MAX_INSTANCES = 64`（WebGL 为 8）
- `CONFIG_MAX_STEREOSCOPIC_EYES = 4` - 立体渲染最大眼数

## 依赖关系
- `backend/DriverEnums.h`
- `utils/BitmaskEnum.h`、`utils/FixedCapacityVector.h`
