# HwDescriptorSetLayoutFactory.h / HwDescriptorSetLayoutFactory.cpp

## 文件概述

实现描述符集布局（Descriptor Set Layout）的工厂类，通过去重和引用计数机制管理 GPU 端的描述符集布局资源。相同布局参数的创建请求会复用已有的 GPU 资源，减少冗余的 GPU 对象创建。

## 核心类/结构体/函数

### `HwDescriptorSetLayoutFactory` 类

- **`create(DriverApi&, DescriptorSetLayout)`** -- 创建或复用描述符集布局。先按 binding 排序描述符，然后在 Bimap 中查找。如果已存在则增加引用计数并返回已有句柄。
- **`destroy(DriverApi&, Handle)`** -- 减少引用计数，当计数归零时真正销毁 GPU 资源。
- **`terminate(DriverApi&)`** -- 终止工厂，断言所有资源已被释放。

### `Parameters` 结构体

- `dsl` -- 描述符集布局描述
- `hash()` -- 使用 murmur 哈希计算参数的哈希值

### 内部结构

- **`Key`** -- 带引用计数的键，包含 `Parameters` 和 `refs` 计数器。
- **`Value`** -- 值，包含 GPU 句柄。
- **`PoolAllocatorArena`** -- 基于池分配器的内存区域（约 1K 条目后回退到堆）。

## 关键实现逻辑

- **去重策略**：创建时先排序描述符（按 binding），然后计算哈希值在 Bimap 中查找。排序保证相同的布局无论描述符顺序如何都能被识别为相同。
- **引用计数**：通过 Key 中的 mutable `refs` 字段管理。创建时 refs=1，复用时 refs++，销毁时 refs--，归零时从 Bimap 中删除并销毁 GPU 资源。
- **Bimap 双向查找**：销毁时通过 Handle 值反向查找 Key，高效定位需要更新引用计数的条目。

## 依赖关系

- `Bimap.h` -- 双向映射容器
- `backend/DriverApi.h` -- GPU 驱动 API
- `utils/Allocator.h`、`utils/Hash.h` -- 内存分配和哈希

## 被引用关系

由 `FEngine` 持有，在材质系统创建描述符集布局时调用，确保相同的布局在 GPU 端只创建一次。
