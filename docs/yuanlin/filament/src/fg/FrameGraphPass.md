# FrameGraphPass

## 文件概述

`FrameGraphPass.h` / `FrameGraphPass.cpp` 定义了帧图通道的执行器层级结构，是帧图中用户回调的载体。

## 核心类/结构体

- **`FrameGraphPassExecutor`**: 抽象基类，定义纯虚的 `execute()` 方法。
- **`FrameGraphPassBase`**: 继承自 `FrameGraphPassExecutor`，持有关联的 `PassNode*` 指针，作为所有通道基类。
- **`FrameGraphPass<Data, Execute>`**: 模板类（final），组合用户数据 `Data` 和执行 lambda `Execute`。
  - 构造时捕获 execute lambda（通过 move）。
  - `execute()` 支持两种签名：带和不带 `DriverApi&` 参数（通过 `if constexpr` 和 `is_invocable_v` 在编译期选择）。
  - `getData()` 和 `operator->` 提供对用户数据的访问。

## 关键实现逻辑

- 通过 Arena 分配器创建（不使用 new），生命周期由 FrameGraph 管理。
- `FrameGraph::addPass` 中对 Execute lambda 大小有 2048 字节限制（`static_assert`），防止捕获过多数据。
- `.cpp` 文件仅包含析构函数的默认实现，以确保虚表在单一编译单元中生成。

## 依赖关系

- `fg/FrameGraphResources.h` - 资源访问接口
- `backend/DriverApiForward.h` - 后端驱动 API 前向声明
- `utils/Allocator.h` - Arena 分配器友元声明
