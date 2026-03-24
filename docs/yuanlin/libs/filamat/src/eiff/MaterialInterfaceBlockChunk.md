# MaterialInterfaceBlockChunk.h / MaterialInterfaceBlockChunk.cpp

## 文件概述

定义了一组 Chunk 子类，用于将材质的接口块信息（Uniform、Sampler、Subpass、常量、描述符绑定等）序列化到 EIFF 二进制格式中。这些接口块描述了着色器与引擎之间的数据传递接口。

## 核心类

### `MaterialUniformInterfaceBlockChunk`
序列化材质的 Uniform 接口块（`BufferInterfaceBlock`）。写入块名称，然后逐字段写入名称、大小、类型、精度和关联采样器。

### `MaterialSamplerInterfaceBlockChunk`
序列化材质的采样器接口块（`SamplerInterfaceBlock`）。写入块名称，然后逐采样器写入名称、绑定点、类型、格式、精度、是否可过滤、是否多重采样和变换名称。

### `MaterialSubpassInterfaceBlockChunk`
序列化子通道（Subpass）信息。写入块名称、有效性标志，以及子通道的名称、类型、格式、精度、附件索引和绑定点。

### `MaterialConstantParametersChunk`
序列化材质的特化常量列表。写入数量后逐条写入名称、类型和默认值。

### `MaterialPushConstantParametersChunk`
序列化推送常量（Push Constant）列表。写入结构体变量名、数量，然后逐条写入名称、类型和着色器阶段。

### `MaterialBindingUniformInfoChunk`
序列化绑定点与 Uniform 的详细映射关系。写入绑定数量，逐绑定写入索引、名称和 Uniform 列表（含名称、偏移、大小、类型）。

### `MaterialAttributesInfoChunk`
序列化顶点属性信息。写入属性数量和每个属性的名称及位置。

### `MaterialDescriptorBindingsChuck`
序列化描述符绑定名称映射。写入描述符数量（采样器 + 1 个 UBO），每个描述符写入名称、类型和绑定点。

### `MaterialDescriptorSetLayoutChunk`
序列化描述符集布局。写入描述符数量，每个描述符写入类型、着色器阶段标志、绑定点、标志位和保留字段。

## 关键实现逻辑

所有类均继承自 `Chunk`，通过 `flatten()` 实现序列化。各类按照 Filament 引擎的二进制材质格式规范，将接口块中的字段信息逐一写入 Flattener。描述符相关的块使用 `DescriptorSets` 工具类来查询描述符名称和类型。

## 依赖关系

- `Chunk.h` -- 基类
- `<backend/Program.h>` -- `Program::UniformInfo`
- `<private/filament/BufferInterfaceBlock.h>` / `SamplerInterfaceBlock.h` -- 接口块定义
- `<private/filament/ConstantInfo.h>` / `PushConstantInfo.h` / `SubpassInfo.h` -- 常量与子通道信息
- `<private/filament/DescriptorSets.h>` -- 描述符集工具
- `<utils/CString.h>` / `<utils/FixedCapacityVector.h>` -- 工具类
