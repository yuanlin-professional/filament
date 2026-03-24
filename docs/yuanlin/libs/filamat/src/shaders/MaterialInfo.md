# MaterialInfo.h

## 文件概述
定义 `MaterialInfo` 结构体，汇总材质编译过程中的所有中间信息，在 `MaterialBuilder::prepareToBuild()` 中填充，在着色器生成和序列化阶段被广泛使用。

## 核心类/结构体/函数
- **`MaterialInfo`** - 材质编译信息聚合结构体，包含：
  - 布尔标志：isLit、hasDoubleSidedCapability、hasExternalSamplers、specularAntiAliasing 等
  - 渲染属性：blendingMode、shading、refractionMode、reflectionMode、quality 等
  - 接口块：`uib`（BufferInterfaceBlock）、`sib`（SamplerInterfaceBlock）、`subpass`（SubpassInfo）
  - SSBO 缓冲区列表：`buffers`
  - 特性级别：featureLevel、stereoscopicType、groupSize 等

## 关键实现逻辑
纯数据聚合结构体，无逻辑。`BufferContainer` 使用 `FixedCapacityVector` 限制最大 SSBO 数量。

## 依赖关系
- `backend/DriverEnums.h`
- `filament/MaterialEnums.h`
- `private/filament/BufferInterfaceBlock.h`、`SamplerInterfaceBlock.h`、`SubpassInfo.h`
