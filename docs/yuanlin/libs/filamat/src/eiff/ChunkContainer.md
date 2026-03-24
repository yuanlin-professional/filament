# ChunkContainer.h / ChunkContainer.cpp

## 文件概述

`ChunkContainer` 是 EIFF 格式的顶层容器，管理一组 `Chunk` 子对象。它负责将所有子块按顺序序列化（flatten）为连续的二进制数据流，每个子块前写入类型标识和大小信息。

## 核心类

### `ChunkContainer`

- **`push<T>(Args&&...)`**: 模板方法，创建一个 `Chunk` 子类实例并添加到容器中。使用 `std::enable_if` 约束 `T` 必须继承自 `Chunk`。
- **`emplace<T>(Args&&...)`**: 便捷方法，专门用于添加 `SimpleFieldChunk<T>` 类型的简单字段块。
- **`getSize()`**: 通过 DryRunner 模式执行一次"干跑"（不实际写入数据），计算序列化后的总字节数。
- **`flatten(Flattener& f)`**: 遍历所有子块，依次写入 `ChunkType`（uint64）、大小占位符、子块数据和实际大小。

### 成员变量

| 成员 | 类型 | 说明 |
|------|------|------|
| `mChildren` | `std::vector<std::unique_ptr<Chunk>>` | 子块列表，使用智能指针管理生命周期 |

## 关键实现逻辑

序列化格式为：`[ChunkType(8字节)] [Size(4字节)] [Chunk数据...]`，对每个子块重复此结构。`getSize()` 通过 `Flattener::getDryRunner()` 执行不写入数据的模拟序列化来获取总大小，开销较大，应仅在最终序列化前调用一次。

## 依赖关系

- `Chunk.h` / `SimpleFieldChunk.h` -- 子块类型
- `Flattener.h` -- 序列化写入器
- `<filament/MaterialChunkType.h>` -- 块类型枚举
- `<memory>` / `<vector>` -- 智能指针与容器
