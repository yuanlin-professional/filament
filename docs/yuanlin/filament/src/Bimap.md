# Bimap.h

## 文件概述

实现一个半泛型的双向映射（Bidirectional Map）容器。该容器存储键值对并支持 O(1) 时间复杂度的双向查找——既可以通过键查找值，也可以通过值查找键。针对大键小值的场景做了优化，键存储在堆外（out-of-line）且不会被移动。

## 核心类/结构体/函数

### `Bimap<Key, Value, KeyHash, ValueHash, Allocator>` 模板类

- **`insert(Key const&, Value const&)`** -- 插入键值对，不允许重复。键通过自定义分配器分配并拷贝构造。
- **`find(Key const&)`** -- 通过键查找值迭代器，O(1) 复杂度。
- **`findValue(Value const&)`** -- 通过值查找键迭代器，O(1) 复杂度。
- **`erase(Key const&)` / `erase(iterator)`** -- 从两个映射中同时删除条目，并销毁和释放键的内存。
- **`clear()`** -- 清空所有条目，遍历前向映射并手动析构和释放所有键。

### 内部结构

- **`KeyDelegate`** -- 键的代理结构体，存储指向键的指针以避免键的拷贝和移动。
- **`ForwardMap`** -- 使用 `tsl::robin_map` 实现的前向映射（Key -> Value）。
- **`BackwardMap`** -- 使用 `tsl::robin_map` 实现的反向映射（Value -> Key）。

## 关键实现逻辑

- **键的间接存储**：键通过分配器在堆上分配，映射中只存储指向键的指针（`KeyDelegate`），避免了键的移动和拷贝开销，特别适合大型键对象。
- **Robin Hood 哈希**：底层使用 `tsl::robin_map`，一种开放寻址的哈希表实现，具有良好的缓存局部性。
- **不可拷贝/移动**：Bimap 禁止了拷贝和移动操作，因为键的生命周期由容器管理。

## 依赖关系

- `tsl/robin_map.h` -- Robin Hood 哈希表实现
- `utils/debug.h` -- 调试断言

## 被引用关系

被 `HwDescriptorSetLayoutFactory`、`HwRenderPrimitiveFactory`、`HwVertexBufferInfoFactory` 等硬件资源工厂类使用，用于实现 GPU 资源的去重和引用计数管理。
