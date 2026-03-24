# StructureOfArrays.h

## 文件概述
Structure-of-Arrays (SoA) 容器模板，将多个类型的数组打包管理，提供缓存友好的数据布局。

## 核心类/结构体/函数
- **StructureOfArraysBase<Allocator, Elements...>**: SoA 基类模板
  - 管理多个类型数组的分配/释放/调整大小
  - `elementAt<N>(i)`: 访问第 N 个数组的第 i 个元素
  - `push_back()` / `pop_back()`: 同步操作所有数组
  - `swap()`: 交换两个位置的所有数组元素
- **StructureOfArrays<Elements...>**: 使用堆分配的 SoA

## 关键实现逻辑
- 所有数组存储在单次分配的连续内存块中，减少内存碎片
- 使用 std::tuple 和模板元编程管理不同类型的数组
- 用于 SingleInstanceComponentManager 存储 ECS 组件数据

## 依赖关系
- `utils/Allocator.h`, `utils/compiler.h`, `utils/Slice.h`
