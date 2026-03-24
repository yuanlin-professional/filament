# MetalResourceTracker

## 文件概述

MetalResourceTracker 是一个简单的资源生命周期跟踪器，将资源关联到特定的命令缓冲区。当命令缓冲区执行完成后，调用 `clearResources` 释放该缓冲区关联的所有资源。确保 GPU 资源在 GPU 使用完毕前不会被提前释放。

**源文件**: `MetalResourceTracker.h`, `MetalResourceTracker.cpp`

## 核心类/结构体

- **`MetalResourceTracker`** - 资源跟踪器
  - `CommandBuffer` - 命令缓冲区标识（void*）
  - `Resource` - 资源标识（const void*）
  - `ResourceDeleter` - 资源释放回调函数
  - `trackResource(buffer, resource, deleter)` - 将资源关联到命令缓冲区，返回是否首次跟踪
  - `clearResources(buffer)` - 释放命令缓冲区的所有关联资源

## 关键实现逻辑

- **数据结构**: 使用 `tsl::robin_map<CommandBuffer, robin_set<ResourceEntry>>` 两层映射。外层按命令缓冲区索引，内层为该缓冲区关联的资源集合
- **资源去重**: `ResourceEntry` 通过资源指针比较实现去重，同一资源只会被跟踪一次
- **线程安全**: 所有操作通过 `std::mutex` 保护，因为 `trackResource`（引擎线程）和 `clearResources`（Metal 完成回调线程）可能并发调用
- **释放顺序**: `clearResources` 遍历资源集合逐一调用 deleter，然后从 map 中移除该命令缓冲区条目

## 依赖关系

- `tsl/robin_map.h`、`tsl/robin_set.h` - 高性能哈希容器
- `utils/debug.h` - 断言宏
