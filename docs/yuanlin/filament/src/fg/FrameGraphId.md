# FrameGraphId

## 文件概述

`FrameGraphId.h` 定义了帧图系统中资源句柄的类型体系，包括无类型的 `FrameGraphHandle` 和类型化的 `FrameGraphId<T>` 模板。

## 核心类/结构体

- **`FrameGraphHandle`**: 无类型的资源句柄基类。
  - `index`: 16 位索引，指向 FrameGraph 中的 `ResourceSlot`，`UNINITIALIZED`（0xFFFF）表示未初始化。
  - `version`: 16 位版本号，用于检测过期句柄。
  - 提供 `isInitialized()`、`getIndex()`、`clear()`、比较运算符等方法。
  - 构造函数为 private，只有 friend 类（FrameGraph、Blackboard 等）可以创建。

- **`FrameGraphId<RESOURCE>`**: 类型化的资源句柄模板，继承自 `FrameGraphHandle`，提供类型安全的资源引用。

## 关键实现逻辑

- 句柄设计遵循轻量级值语义，可安全复制和比较。
- 版本机制确保过期句柄不会被误用（每次 write 操作递增版本号）。
- `operator bool()` 允许直接用于条件判断。

## 依赖关系

- `<stdint.h>`, `<limits>`, `<utility>` - 标准库
