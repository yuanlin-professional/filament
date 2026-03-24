# LocalProgramCache.h / LocalProgramCache.cpp

## 文件概述
LocalProgramCache 是材质程序的 L0 级缓存，负责按需管理和重编译着色器程序。每个 Material 和 MaterialInstance 拥有自己的 LocalProgramCache 实例，用于缓存已编译的 GPU 程序句柄，避免重复编译。

## 核心类/结构体/函数
- **LocalProgramCache** - 材质程序本地缓存类
  - `initializeForMaterial()` - 为 Material 初始化缓存，设置特化常量并获取已有程序
  - `initializeForMaterialInstance()` - 为 MaterialInstance 初始化，从其 Material 复制特化常量
  - `prepareProgram()` - 准备指定变体的程序，快速路径直接返回缓存，慢路径调用 `prepareProgramSlow()`
  - `getProgram()` - 获取已缓存的程序句柄（必须在 prepareProgram 之后调用）
  - `setConstants()` - 通过 ID 或名称设置特化常量，触发程序重新编译
  - `getConstant()` - 获取特化常量值（支持 int32_t / float / bool）
  - `terminate()` / `clear()` - 释放缓存资源

## 关键实现逻辑
- 采用**两级缓存策略**：LocalProgramCache 是 L0 缓存，MaterialCache 的 ProgramCache 是全局缓存
- `prepareProgramSlow()` 区分共享变体（如深度变体使用默认材质的程序）和非共享变体
- 特化常量通过 InternPool 管理，修改常量时会释放旧程序并获取新程序
- `filterVariantForGetProgram()` 处理 FOG 后处理特性标志的变体过滤
- 拷贝构造函数不拷贝已缓存的程序，仅拷贝特化常量（通过 InternPool 获取引用）

## 依赖关系
- `MaterialDefinition.h` - 材质定义
- `MaterialParser.h` - 材质解析器
- `details/Engine.h` / `details/Material.h` - 引擎和材质内部实现
- `backend/Handle.h` / `backend/Program.h` - 后端程序句柄

## 被引用关系
- `FMaterial` - Material 持有 LocalProgramCache 用于管理程序缓存
- `FMaterialInstance` - MaterialInstance 持有自己的 LocalProgramCache 副本
