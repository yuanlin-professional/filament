# JsonWriter.h / JsonWriter.cpp

## 文件概述
将 FrameGraphInfo 数据序列化为 JSON 字符串，用于通过 REST API 传输给 Web 客户端。

## 核心类

### `JsonWriter`
- `writeFrameGraphInfo(frameGraph)` - 将帧图信息写入内部缓冲区
- `getJsonString()` / `getJsonSize()` - 获取生成的 JSON 字符串

## 关键实现逻辑
- 使用 `std::ostringstream` 手动构建 JSON（不依赖第三方 JSON 库）
- `writeJSONString` 对特殊字符（引号、反斜杠、换行、制表符）进行转义
- 输出结构：`viewName` + `passes`（数组，含 reads/writes/renderTargets）+ `resources`（对象映射）+ `graphviz`（DOT 字符串）
- renderTargets 的每个条目包含 discardStart/discardEnd/clear 三个 attachment 数组

## 依赖关系
- `fgviewer/FrameGraphInfo.h` - 帧图数据结构
- `utils/CString.h` - 结果存储
