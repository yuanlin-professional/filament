# FrameGraphInfo.h / FrameGraphInfo.cpp

## 文件概述
帧图信息的数据模型，描述一个视图的完整帧图结构，包括渲染 Pass、资源及其依赖关系。

## 核心类

### `FrameGraphInfo`
- **构造函数**: `FrameGraphInfo(viewName)` - 创建指定视图名称的空帧图信息
- `setPasses(sortedPasses)` - 设置按执行顺序排列的 Pass 列表
- `setResources(resources)` - 设置资源映射表
- `setGraphvizData(data)` - 设置 Graphviz DOT 格式的可视化数据
- `operator==` - 比较两个帧图信息是否相同（跳过 graphviz 数据）

### `FrameGraphInfo::Pass`
描述一个渲染 Pass：
- `id` - 唯一标识
- `name` - 名称
- `reads` / `writes` - 读取和写入的资源 ID 列表
- `renderTargets` - 渲染目标信息列表，每个包含 discardStart/discardEnd/clear 的附件信息

### `FrameGraphInfo::Resource`
描述一个资源：
- `id` - 唯一标识
- `name` - 名称
- `properties` - 键值对属性列表（灵活适配不同资源类型）

## 依赖关系
- `utils/CString.h` - 字符串类型
