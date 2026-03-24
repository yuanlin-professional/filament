# VulkanQueryManager (VulkanQueryManager.h / VulkanQueryManager.cpp)

## 文件概述

Vulkan 时间戳查询管理器，管理 VkQueryPool 及其时间戳查询的分配、录制和结果获取。用于 GPU 性能计时。

## 核心类/结构体

### `VulkanQueryManager` 类
查询管理器：
- `getNextQuery(resourceManager)` - 分配一对查询索引（起始+停止），创建 VulkanTimerQuery
- `clearQuery(query)` - 释放查询索引
- `beginQuery(commands, query)` - 在命令缓冲区中录制起始时间戳
- `endQuery(commands, query)` - 录制停止时间戳
- `getResult(query)` - 获取查询结果（含可用性标志）
- `terminate()` - 销毁 VkQueryPool

### `QueryResult` 结构体
查询结果：beginTime、beginAvailable、endTime、endAvailable（各 64 位）。

## 关键实现逻辑

- **索引分配**: 使用 bitset32 追踪已使用的查询索引，通过位操作快速找到空闲索引
- **成对分配**: 每个 TimerQuery 占用两个连续索引（起始和停止）
- **线程安全**: 索引分配和释放通过 Mutex 保护
- **可用性查询**: getResult 使用 `VK_QUERY_RESULT_WITH_AVAILABILITY_BIT` 标志

## 依赖关系

- `vulkan/memory/ResourcePointer.h` - resource_ptr<VulkanTimerQuery>
