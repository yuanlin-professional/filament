# Material

## 文件概述

`FMaterial` 是 `Material` 公共 API 的私有实现类，是 Filament 材质系统的核心。它封装了从 .filamat 材质包解析出的所有材质定义数据，管理着色器程序的编译/缓存，以及材质实例的创建。支持多种变体（Variant）的着色器管理和 matdbg 实时调试。

## 核心类/结构体

### `FMaterial`
- 继承自 `Material`
- 持有 `MaterialDefinition` 引用（包含 uniform/sampler 接口块、描述符集布局等）
- `LocalProgramCache mPrograms` -- 管理各变体的已编译着色器程序
- 支持默认材质（`DefaultMaterialBuilder`）、深度变体预缓存、共享变体机制

## 关键实现逻辑

- **变体管理** -- 通过 `Variant` 位掩码管理不同渲染状态组合（雾、VSM、深度等），`isSharedVariant()` 判断是否使用默认材质的深度着色器
- **程序获取** -- `prepareProgram()` 预编译 -> `getProgram()` 获取句柄，支持雾效后处理特性标志
- **描述符集布局** -- `getDescriptorSetLayout()` 根据变体返回材质自身或默认材质的布局
- **matdbg 调试** -- 条件编译支持实时材质替换（`onEditCallback`）和活跃变体查询（`onQueryCallback`）
- **UBO 批处理** -- `useUboBatching()` 标志控制是否使用共享 UBO

## 依赖关系

- `LocalProgramCache.h` -- 着色器程序缓存
- `details/MaterialInstance.h` -- 材质实例管理
- `ds/DescriptorSetLayout.h` -- 描述符集布局
- `private/filament/Variant.h` -- 变体系统
- `private/filament/BufferInterfaceBlock.h`、`SamplerInterfaceBlock.h` -- 着色器接口定义

## 与公共 API 的关系

直接对应 `filament::Material`。通过 `Material::Builder` 从 .filamat 材质包构建。管理 `MaterialInstance` 的创建，是渲染管线中材质绑定的核心。
