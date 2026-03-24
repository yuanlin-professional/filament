# MaterialCache.h / MaterialCache.cpp

## 文件概述
MaterialCache 是 Filament 引擎级的材质缓存系统，负责对材质定义和编译后的 GPU 程序进行引用计数管理和全局缓存。它确保相同的材质数据不会被重复解析，相同特化参数的着色器程序不会被重复编译。

## 核心类/结构体/函数
- **MaterialCache** - 全局材质缓存管理器
  - `acquireMaterial()` - 获取或创建材质定义条目，使用 CRC32 作为哈希键
  - `releaseMaterial()` - 释放材质定义引用，引用计数归零时释放 GPU 资源
  - `getProgramCache()` - 获取全局程序缓存（`RefCountedMap<ProgramSpecialization, Handle>`）
  - `getSpecializationConstantsInternPool()` - 获取特化常量内化池
  - `terminate()` - 清理 LRU 缓存中的遗留资源
- **MaterialKey** - 材质缓存键，使用 MaterialParser 的 CRC32 作为哈希
  - `Hash::operator()` - 先尝试从材质文件中读取 CRC32，失败则计算

## 关键实现逻辑
- 使用 `RefCountedMap` 实现引用计数缓存：材质定义和程序都支持 acquire/release 语义
- MaterialKey 使用 `unique_ptr<MaterialDefinition>` 存储值，确保指针稳定性
- `terminate()` 需要在 MaterialCache 析构前显式调用，以清理 LRU 缓存中尚存的资源
- 析构时断言所有资源已释放（mDefinitions、mPrograms、mSpecializationConstantsInternPool 均为空）

## 依赖关系
- `MaterialDefinition.h` / `ProgramSpecialization.h` - 材质定义和程序特化
- `MaterialParser.h` - 材质解析器
- `utils/InternPool.h` / `utils/RefCountedMap.h` - 数据结构

## 被引用关系
- `FEngine` - 引擎持有 MaterialCache 实例
- `LocalProgramCache` - 通过 MaterialCache 的 ProgramCache 管理全局程序缓存
- `MaterialDefinition` - 通过 MaterialCache 获取和释放程序
