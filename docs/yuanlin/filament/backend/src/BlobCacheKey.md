# BlobCacheKey

## 文件概述

`BlobCacheKey.h` / `BlobCacheKey.cpp` 实现了用于 GPU 程序二进制缓存的键生成机制。将程序 ID 和特化常量组合编码为一个二进制键，供 `Platform::insertBlob`/`retrieveBlob` 缓存系统使用。

## 核心类/结构体

### `BlobCacheKey`
- 封装了一个变长二进制键，由程序 ID（uint64_t）和特化常量数组组成。
- **data()** / **size()**: 返回键的原始数据指针和大小。
- **operator bool()**: 判断键是否有效（size > 0）。
- **swap()**: 高效交换两个键对象。
- 内部使用 `shared_ptr<Key>` 管理内存，以便与 `std::function<>` 兼容（因缓冲区不可变）。

### `BlobCacheKey::Key`（内部结构体）
- 柔性数组成员（flexible array member）结构：`uint64_t id` 后跟 `Program::SpecializationConstant constants[]`。
- 通过 `malloc` 分配精确大小的内存，`memset` 清零确保确定性哈希。

## 关键实现逻辑

- 构造时计算所需内存大小：`sizeof(Key) + sizeof(常量元素) * 常量数量`。
- 使用 `shared_ptr` 配合自定义删除器（`::free`）管理 C 风格动态分配的内存。
- 移动语义实现：转移 `shared_ptr` 所有权并将源对象的 `mSize` 置零。

## 依赖关系

- `backend/Program.h` - `Program::SpecializationConstant` 类型定义
- `utils::FixedCapacityVector` - 特化常量容器类型
