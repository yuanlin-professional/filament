# MaterialInstanceManager.h / MaterialInstanceManager.cpp

## 文件概述
MaterialInstanceManager 管理一个基于 Material 映射的 MaterialInstance 缓存池。它允许跨帧复用 MaterialInstance，减少反复创建销毁带来的开销，主要用于引擎内部的后处理等系统。

## 核心类/结构体/函数
- **MaterialInstanceManager** - 材质实例缓存管理器
  - `getMaterialInstance(FMaterial*, uint32_t tag)` - 根据材质和标签获取或创建实例（有标签的缓存）
  - `getMaterialInstance(FMaterial*)` - 从匿名池获取实例（无标签的临时使用）
  - `reset()` - 重置匿名实例池的高水位标记，使已有实例可被复用
  - `terminate()` - 销毁所有缓存的实例
- **Key** - 缓存键（material 指针 + tag）
- **AnonymousPool** - 匿名实例池，使用 `highWaterMark` 跟踪当前使用量

## 关键实现逻辑
- **两种缓存模式**：
  1. 带标签缓存：使用 `unordered_map<Key, FMaterialInstance*>` 按 (material, tag) 键缓存
  2. 匿名缓存：使用 `unordered_map<FMaterial*, AnonymousPool>` 按材质分组，通过 highWaterMark 实现对象池复用
- 匿名池每帧调用 `reset()` 将 highWaterMark 重置为 0，下一帧可复用之前创建的实例
- 匿名池在 highWaterMark 超出已有实例数时才创建新实例

## 依赖关系
- `details/Engine.h` / `details/Material.h` - 引擎和材质内部实现
- `utils/Hash.h` - 哈希工具

## 被引用关系
- `PostProcessManager` - 使用 MaterialInstanceManager 管理后处理材质实例
