# MaterialDefinition.h / MaterialDefinition.cpp

## 文件概述
MaterialDefinition 是对材质文件的解析结果，是一个纯只读的数据结构，包含材质的所有属性、着色器变体、描述符集布局和特化常量等信息。它不包含可变状态，被多个 Material 实例共享。

## 核心类/结构体/函数
- **MaterialDefinition** - 已解析的材质定义（结构体，成员几乎全部公开）
  - `compileProgram()` - 无条件编译指定变体的着色器程序
  - `prepareProgram()` - 先查询 MaterialCache，缓存未命中时才编译
  - `acquirePrograms()` / `releasePrograms()` - 批量获取/释放程序缓存条目
  - `hasVariant()` - 检查指定变体是否存在
  - `getVariants()` / `getDepthVariants()` - 获取材质支持的变体列表
  - `terminate()` - 释放描述符集布局等 GPU 资源
  - 主要成员：`rasterState`、`blendingMode`、`shading`、`materialDomain`、`samplerInterfaceBlock`、`uniformInterfaceBlock`、`specializationConstants`、`pushConstants`

## 关键实现逻辑
- **构造过程**分为五个阶段：`processMain()`、`processBlendingMode()`、`processSpecializationConstants()`、`processPushConstants()`、`processDescriptorSets()`
- **混合模式处理**：根据 BlendingMode（OPAQUE/TRANSPARENT/ADD/MULTIPLY/SCREEN/CUSTOM）设置不同的混合函数和深度写入
- **特化常量**：预留 `CONFIG_MAX_RESERVED_SPEC_CONSTANTS` 个系统常量（如后端特性级别、Froxel 缓冲区大小、立体渲染眼数等），其余为用户自定义常量
- **程序编译**：`getSurfaceProgram()` 分离顶点和片段变体，`getProgramWithVariants()` 从 MaterialParser 获取着色器代码并构建 Program 对象
- **缓存策略**：`acquirePrograms()` 和 `releasePrograms()` 使用模板参数 `useCache` 控制是否使用全局缓存

## 依赖关系
- `MaterialParser.h` - 从材质文件中读取数据
- `ProgramSpecialization.h` - 程序特化标识
- `Froxelizer.h` - 获取 Froxel 缓冲区大小
- `ds/ColorPassDescriptorSet.h` - 描述符集布局
- `backend/Program.h` / `backend/DriverEnums.h` - 后端程序和枚举

## 被引用关系
- `MaterialCache` - 通过引用计数管理 MaterialDefinition 的生命周期
- `FMaterial` - 持有 MaterialDefinition 的引用
- `LocalProgramCache` - 调用 MaterialDefinition 的 acquirePrograms/releasePrograms
